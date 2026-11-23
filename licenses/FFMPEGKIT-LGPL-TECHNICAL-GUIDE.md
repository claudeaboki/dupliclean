# DupliClean 1.2.3 (20) — Guide technique LGPL / LGPL technical guide

**Portée / Scope** : paquet Android `com.dupliclean.app`, FFmpegKit Android `min` `8.1.2`, JCodec `0.2.5`.

**Statut / Status** : ce guide documente les sources de référence et une démonstration technique de reconditionnement. Il **ne certifie pas la conformité complète** : un remplacement fonctionnel reconstruit à partir des sources et la mise à disposition d'une archive exhaustive des sources correspondantes restent à finaliser. / This guide documents pinned upstream sources and a repackaging demonstration. It **does not certify full compliance**: a functional replacement rebuilt from source and complete corresponding-source delivery remain outstanding.

## Français

### Sources précisément identifiées

- **FFmpegKit Android** : [sources au commit `d28006b5b718a324b89962e70feedc4512c5942a`](https://github.com/maitrungduc1410/ffmpeg-kit/tree/d28006b5b718a324b89962e70feedc4512c5942a) (tag `android-8.1.2`).
- **FFmpeg** : [sources au commit `3a0867c2bfda4a4d4309ca1a8cbdc6175e67f587`](https://github.com/arthenica/FFmpeg/tree/3a0867c2bfda4a4d4309ca1a8cbdc6175e67f587), tag `n7.1.5`, tel que déclaré dans `scripts/source.sh` du fournisseur.
- **Modifications ajoutées par les scripts FFmpegKit** : voir `scripts/android/ffmpeg.sh` (modifications de `libavutil/log.c` et fichiers des protocoles `tools/protocols` entre autres), avec les scripts de la même révision.
- **Configuration déclarée pour Android** : workflow `.github/workflows/android-build-scripts.yml`, NDK `r27c`, variante `min`, paramètres communs `--enable-android-media-codec --enable-android-zlib --disable-arm-v7a --disable-x86 --disable-x86-64`. Le script FFmpeg demande `--disable-static --enable-shared --enable-version3`; `--enable-gpl` n'est appliqué que si son option a été explicitement demandée. **La configuration réelle de la compilation publiée n'est pas encore établie à partir du `ffbuild/config.log` d'origine.**
- **Binaires de référence** : [AAR du fournisseur](https://github.com/maitrungduc1410/ffmpeg-kit/releases/tag/android-8.1.2). SHA-256 de l'AAR `min` : `53e3f17a8971eabc03910db1e6d59366ca1961031f98ef3f84ec925cb1fe478c`.

Les rapports locaux DUP071-20/21 ont confirmé que les **huit fichiers ARM64 FFmpeg** dans l'AAB de production et l'AAR du fournisseur ont des empreintes SHA-256 identiques. Ce contrôle ne vaut pas reproduction de la compilation.

### Remplacer une bibliothèque native dans une application Android

Les bibliothèques sont chargées depuis un paquet APK Android signé. Une personne disposant légalement d'un APK de l'application et du SDK Android peut, **sur un appareil qu'elle contrôle**, tenter de :

1. récupérer les APK de l'application installée (selon le téléphone, il peut y avoir un APK de base et des APK fractionnés) ou un APK universel compatible ;
2. reconstruire une version ABI-compatible de la bibliothèque LGPL à partir des sources et scripts indiqués ci-dessus, en conservant les symboles requis ;
3. réassembler le ou les APK avec le fichier `.so` de remplacement, sans toucher aux autres composants ;
4. effectuer l'alignement et signer le résultat avec **sa propre clé de test** (`zipalign` puis `apksigner`) ;
5. vérifier la signature, puis désinstaller l'original si sa signature est différente et installer l'APK modifié ; cela peut effacer les données locales de l'application. La clé de signature Google Play ne peut pas être partagée et n'est pas nécessaire pour une installation indépendante autorisée par Android.

Le fait de pouvoir réassembler et signer un APK ne signifie **pas** qu'une version ABI-compatible reconstruite fonctionnera. Les essais peuvent aussi être affectés par des splits, la signature Play, la version Android, les limitations du téléphone et les règles de distribution. Cette description ne fournit pas, à elle seule, toutes les informations d'installation qui pourraient être exigées par LGPL-3.0 §4(e) et GPL-3.0 §6.

### Ce qui a été démontré

Le 22 novembre 2026, un essai local **DUP071-22D** a utilisé un APK universel généré depuis l'AAB `1.2.3 (20)`, puis a remplacé `lib/arm64-v8a/libavutil.so` par un ELF contenant **une section de diagnostic non chargée**, sans modification fonctionnelle du décodeur. L'APK a été réaligné à 16 Kio, signé avec une **clé Debug existante** et sa signature vérifiée. Le développeur a confirmé que l'installation sur Samsung et les scans fonctionnaient. **Ce n'est pas un test de reconstruction du codec depuis une modification de code source.**

L'AAB de production original n'a pas été modifié lors de cette opération.

### Obligations encore ouvertes avant déclaration de conformité

- Publier durablement une **archive téléchargeable complète** des sources réellement correspondantes, incluant les scripts et les changements de construction, pas seulement des liens ou notices.
- Récupérer ou reproduire les informations de compilation effectives (`ffbuild/config.log`, liste de composants actifs, versions, paramètres, absence de composants GPL/non libres).
- Vérifier l'exercice des droits de modification/remplacement d'une bibliothèque **réellement reconstruite** et les informations d'installation à fournir, avec revue juridique ciblée si besoin.
- Donner dans l'application un avis clair FFmpegKit/FFmpeg et JCodec, et un accès aux textes de licences et sources ; l'audit de l'APK expérimental a constaté que `assets/flutter_assets/NOTICES.Z` ne contenait aucune mention de ces composants.

**Aucune clé privée, code source propriétaire ni média utilisateur n'est publié ici.** Pour les demandes de sources correspondantes : [claudeaboki@gmail.com](mailto:claudeaboki@gmail.com).

---

## English (summary)

The precise upstream references are **FFmpegKit commit `d28006b5b718a324b89962e70feedc4512c5942a`** and **FFmpeg tag `n7.1.5` / commit `3a0867c2bfda4a4d4309ca1a8cbdc6175e67f587`**, with FFmpegKit's source modifications and Android workflow at the pinned commit.

The eight arm64 FFmpeg shared libraries in the candidate AAB were byte-for-byte matched to the distributor's AAR. A test APK was created using a non-loaded ELF diagnostic section in `libavutil.so`, repackaged, signed with an existing independent Android Debug key, installed, and reported working on a Samsung phone. No source-level codec modification or reproducible build was proven.

On Android, modified APKs must be correctly aligned and re-signed. A user may install a separately signed version on a compatible, user-controlled device after removing the differently signed original, subject to the Android version, split APK packaging and device restrictions. This may delete local app data. **This is not a claim that all LGPL-3.0 §4 replacement or installation-information obligations have been satisfied.**

Outstanding: publish complete corresponding sources and applied modifications, verify the actual build configuration and third-party licensing, demonstrate replacement with a library rebuilt from modified sources (or obtain focused professional review), and include notices and accessible license texts in the distributed application.

For source requests: [claudeaboki@gmail.com](mailto:claudeaboki@gmail.com).

References: [FFmpeg licensing checklist](https://ffmpeg.org/legal.html), [LGPL-3.0](https://www.gnu.org/licenses/lgpl-3.0.html).
