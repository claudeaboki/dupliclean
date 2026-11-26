# DupliClean 1.2.3 (20) — Guide technique LGPL / LGPL technical guide

## État actuel au 26 novembre 2026 / Current status (November 26, 2026)

**Ce document concerne le candidat Android 1.2.3 (20), non encore distribué sur Google Play. Les sections historiques ci-dessous décrivent les anciens essais avec l'AAR fournisseur et sont conservées uniquement pour la traçabilité ; elles ne décrivent plus la configuration actuelle.**

**This document concerns the Android 1.2.3 (20) candidate, not yet distributed through Google Play. Historical sections below describe the previous distributor-AAR investigation and are retained for traceability only.**

- [Release publique des sources, licences et preuves / Public source, license and build-evidence release](https://github.com/claudeaboki/DupliClean-LGPL/releases/tag/dupliclean-1.2.3-ffmpegkit-8.1.2), 14 assets verified against the collection manifest.
- FFmpegKit: [e15206ec7b29aff45853571f98308570166f2dca](https://github.com/maitrungduc1410/ffmpeg-kit/tree/e15206ec7b29aff45853571f98308570166f2dca); FFmpeg: [38b88335f99e76ed89ff3c93f877fdefce736c13](https://github.com/arthenica/FFmpeg/tree/38b88335f99e76ed89ff3c93f877fdefce736c13).
- [Baseline rebuild evidence](https://github.com/claudeaboki/DupliClean-LGPL/releases/download/dupliclean-1.2.3-ffmpegkit-8.1.2/ffmpegkit-min-baseline-45522659807.zip); baseline AAR SHA-256 `9acdf35944f494c15028da80781aa3b786373600e4b370b92a4d237c5f113f81`. Configuration evidence: CONFIG_GPL=0, CONFIG_NONFREE=0, CONFIG_LGPLV3=1, CONFIG_SHARED=1, CONFIG_STATIC=0. Not claimed byte-identical to the previous distributor AAR.
- [Source-modified experiment](https://github.com/claudeaboki/DupliClean-LGPL/actions/runs/45527055955): source-level version marker observed in a separately signed experimental APK on Samsung. Not distributed as a production library.
- DUP071-47Q/47T/47U: direct Gradle integration of the 18 baseline native binaries; ARM64 Samsung cold test: 3 WMV2 FFmpegKit and 4 MPEG2 JCodec fingerprints successful, zero specialized decoder failures. DUP071-47V: permanent local AAR integration.
- [Guide de reconstruction et remplacement / Rebuild and replacement guide](https://github.com/claudeaboki/DupliClean-LGPL/releases/download/dupliclean-1.2.3-ffmpegkit-8.1.2/GUIDE-REBUILD-REPLACEMENT.md) and [SHA-256 manifest](https://github.com/claudeaboki/DupliClean-LGPL/releases/download/dupliclean-1.2.3-ffmpegkit-8.1.2/SHA256SUMS.txt).
- CPU Features 0.8.0 (Apache-2.0), Smart Exception Java/Common 0.2.1 (BSD-3-Clause), JCodec 0.2.5 (BSD-2-Clause): see [notices](../third-party-licenses.md).

**Limites / Limits:** the source release and native replacement demonstration do not establish full compliance. The Java FFmpegKit wrapper is merged into optimized DEX. A practical recombination method or suitable alternative under LGPL v3 §4(d), applicable installation information under §4(e), final APK/AAB checks, and validation of the distribution-specific notices remain outstanding. The released source assets are not a complete application recombination kit. The proprietary source and private signing keys are not published. / La recombinaison Java, les informations d'installation applicables et la validation de la distribution finale restent ouvertes. Aucun AAB final ni certification juridique à ce stade.

---

## Archive historique / Historical archive — superseded for the rebuilt candidate

**Portée / Scope** : paquet Android `com.dupliclean.app`, FFmpegKit Android `min` `8.1.2`, JCodec `0.2.5`.

> **Rectification DUP071-28 — 22 novembre 2026 / Critical source-version correction.** Le binaire FFmpeg réellement distribué (`libavcodec.so`, SHA-256 `a01398fde2967e09ba8c01a578094b06fc72273f7c82a02f13e1bf2896afae83`) contient `FFmpeg version n8.1.2`. Or `scripts/source.sh` au commit FFmpegKit `d28006b5...` pointe vers `n7.1.5`. Ce n'est **pas la version source correspondante**. Le code source FFmpeg **n8.1.2**, commit `38b88335f99e76ed89ff3c93f877fdefce736c13`, est la nouvelle base candidate; la révision et les correctifs exacts du build fournisseur ne sont pas encore prouvés. Le script historique `scripts/android/ffmpeg.sh` tente de remplacer `static int av_log_level`; le source FFmpeg 8.1.2 utilise `static atomic_int av_log_level` : il ne faut pas exécuter cette recette en supposant qu'elle produit le binaire publié. / The distributed binary embeds FFmpeg n8.1.2, while the referenced historical build script selects n7.1.5. Do not treat the older script as corresponding source. The source-level patches used by the supplier still require verification.

**Statut / Status** : ce guide documente les sources de référence et une démonstration technique de reconditionnement. Il **ne certifie pas la conformité complète** : un remplacement fonctionnel reconstruit à partir des sources et la mise à disposition d'une archive exhaustive des sources correspondantes restent à finaliser. / This guide documents pinned upstream sources and a repackaging demonstration. It **does not certify full compliance**: a functional replacement rebuilt from source and complete corresponding-source delivery remain outstanding.

## Français

### Sources précisément identifiées

- **FFmpegKit Android** : [sources au commit `d28006b5b718a324b89962e70feedc4512c5942a`](https://github.com/maitrungduc1410/ffmpeg-kit/tree/d28006b5b718a324b89962e70feedc4512c5942a) (tag `android-8.1.2`).
- **FFmpeg présent dans le binaire — source candidate (non certifiée)** : [`arthenica/FFmpeg`, tag `n8.1.2`, commit `38b88335f99e76ed89ff3c93f877fdefce736c13`](https://github.com/arthenica/FFmpeg/tree/38b88335f99e76ed89ff3c93f877fdefce736c13). Le fichier historique `scripts/source.sh` pointe vers `n7.1.5` : **cette référence est incorrecte pour l'AAR publié**.
- **Modifications ajoutées par les scripts FFmpegKit** : voir `scripts/android/ffmpeg.sh` (modifications de `libavutil/log.c` et fichiers des protocoles `tools/protocols` entre autres), avec les scripts de la même révision.
- **Configuration déclarée pour Android** : workflow `.github/workflows/android-build-scripts.yml`, NDK `r27c`, variante `min`, paramètres communs `--enable-android-media-codec --enable-android-zlib --disable-arm-v7a --disable-x86 --disable-x86-64`. Le script FFmpeg demande `--disable-static --enable-shared --enable-version3`; `--enable-gpl` n'est appliqué que si son option a été explicitement demandée. **Cette recette est historiquement incohérente avec la version native 8.1.2 : ne pas l'utiliser comme instructions de reconstruction correspondantes.** La configuration enregistrée dans les binaires a été extraite (DUP071-27), mais le journal `ffbuild/config.log` d'origine demeure indisponible.
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

**Correction:** the shipped binary identifies **FFmpeg n8.1.2**, candidate upstream commit **`38b88335f99e76ed89ff3c93f877fdefce736c13`**. The FFmpegKit commit **`d28006b5b718a324b89962e70feedc4512c5942a`** referenced previously instead selects **n7.1.5** and is **not established as the build source for the supplied AAR**. Exact source-level modifications and build commit still require verification.

The eight arm64 FFmpeg shared libraries in the candidate AAB were byte-for-byte matched to the distributor's AAR. A test APK was created using a non-loaded ELF diagnostic section in `libavutil.so`, repackaged, signed with an existing independent Android Debug key, installed, and reported working on a Samsung phone. No source-level codec modification or reproducible build was proven.

On Android, modified APKs must be correctly aligned and re-signed. A user may install a separately signed version on a compatible, user-controlled device after removing the differently signed original, subject to the Android version, split APK packaging and device restrictions. This may delete local app data. **This is not a claim that all LGPL-3.0 §4 replacement or installation-information obligations have been satisfied.**

Outstanding: publish complete corresponding sources and applied modifications, verify the actual build configuration and third-party licensing, demonstrate replacement with a library rebuilt from modified sources (or obtain focused professional review), and include notices and accessible license texts in the distributed application.

For source requests: [claudeaboki@gmail.com](mailto:claudeaboki@gmail.com).

References: [FFmpeg licensing checklist](https://ffmpeg.org/legal.html), [LGPL-3.0](https://www.gnu.org/licenses/lgpl-3.0.html).
