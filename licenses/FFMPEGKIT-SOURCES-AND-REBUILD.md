# DupliClean 1.2.3 (20) — FFmpegKit / FFmpeg: sources et reconstruction / source and rebuild information

**État / Status : documentation de provenance; conformité LGPL complète NON certifiée / provenance documentation; full LGPL compliance NOT certified.**

Cette page complète les [notices de licences tierces](../third-party-licenses.md) de DupliClean. Elle ne publie pas le code propriétaire de l'application.

This page supplements DupliClean's [third-party license notices](../third-party-licenses.md). It does not publish proprietary application source code.

## 1. Composants identifiés / Identified components

- **FFmpegKit Android (min)** : `io.github.maitrungduc1410:ffmpeg-kit-min:8.1.2` ; projet et scripts de construction / project and build scripts: [commit `d28006b5b718a324b89962e70feedc4512c5942a`](https://github.com/maitrungduc1410/ffmpeg-kit/tree/d28006b5b718a324b89962e70feedc4512c5942a), tag `android-8.1.2`.
- **Archive binaire du fournisseur / Distributor binary AAR** : [`ffmpeg-kit-min-8.1.2.aar`](https://github.com/maitrungduc1410/ffmpeg-kit/releases/download/android-8.1.2/ffmpeg-kit-min-8.1.2.aar), 15 446 429 octets / bytes, SHA-256 `53e3f17a8971eabc03910db1e6d59366ca1961031f98ef3f84ec925cb1fe478c`.
- **Sources FFmpeg sélectionnées par le script / FFmpeg source selected by the script** : [`arthenica/FFmpeg`, tag `n7.1.5`](https://github.com/arthenica/FFmpeg/tree/n7.1.5), tel que spécifié dans [`scripts/source.sh`](https://github.com/maitrungduc1410/ffmpeg-kit/blob/d28006b5b718a324b89962e70feedc4512c5942a/scripts/source.sh).
- **Scripts et modifications de construction / Build scripts and source modifications** : [`android.sh`](https://github.com/maitrungduc1410/ffmpeg-kit/blob/d28006b5b718a324b89962e70feedc4512c5942a/android.sh), [`scripts/android/ffmpeg.sh`](https://github.com/maitrungduc1410/ffmpeg-kit/blob/d28006b5b718a324b89962e70feedc4512c5942a/scripts/android/ffmpeg.sh), [workflow Android](https://github.com/maitrungduc1410/ffmpeg-kit/blob/d28006b5b718a324b89962e70feedc4512c5942a/.github/workflows/android-build-scripts.yml), [`tools/protocols/`](https://github.com/maitrungduc1410/ffmpeg-kit/tree/d28006b5b718a324b89962e70feedc4512c5942a/tools/protocols).
- **JCodec** : `org.jcodec:jcodec:0.2.5`, BSD-2-Clause ; voir les notices principales / see the main notices.

## 2. Configuration publiée par le fournisseur / Distributor-published build configuration

Au commit ci-dessus, le workflow Android définit NDK `r27c-linux`, le profil **min**, et les indicateurs communs `--enable-android-media-codec --enable-android-zlib --disable-arm-v7a --disable-x86 --disable-x86-64`. Le script FFmpeg déclare des bibliothèques partagées (`--disable-static --enable-shared`) et `--enable-version3`. Le fournisseur annonce une version LGPL-3.0 sans composants GPL pour cette release.

At the pinned commit, the Android workflow specifies NDK `r27c-linux`, the **min** variant, and common flags `--enable-android-media-codec --enable-android-zlib --disable-arm-v7a --disable-x86 --disable-x86-64`. The FFmpeg build script specifies shared libraries (`--disable-static --enable-shared`) and `--enable-version3`. The distributor describes this release as LGPL-3.0 without GPL-enabled components.

**Limite / Limitation** : ces scripts documentent une recette, mais nous n'avons pas reproduit indépendamment la construction à partir de ces sources ni vérifié le `config.log` original du fournisseur. Une recette publiée et une archive binaire identifiée ne prouvent pas, à elles seules, l'intégralité des paramètres et modifications effectivement utilisés. / These scripts document a build recipe, but no independent rebuild or inspection of the distributor's original `config.log` has been completed.

## 3. Traçabilité DupliClean / DupliClean traceability

Selon le rapport d'audit local `DUP071-21` (22 novembre 2026, 18:17) / According to the local `DUP071-21` audit report (2026-11-22, 18:17):

- AAB candidat DupliClean `1.2.3 (20)` : **93 449 384 octets**, SHA-256 `e5cc63099d198e364ad825e96b99fc8b6838b926345507986b4b56b35fc18924`.
- Les huit fichiers `arm64-v8a/libavcodec.so`, `libavdevice.so`, `libavfilter.so`, `libavformat.so`, `libavutil.so`, `libffmpegkit.so`, `libswresample.so`, `libswscale.so` ont chacun le **même SHA-256 dans l'AAB, le cache Gradle et l'AAR du fournisseur**. / These eight ARM64 files had matching individual SHA-256 values in the AAB, Gradle cache and distributor AAR.
- Le fichier AAR du cache Gradle est différent au niveau **archive** (15 438 247 octets; SHA-256 `a653e08aec52d6183755623aa451b891663a2bcdd4d50faaf5dd2c9bc721827b`), malgré l'identité des huit bibliothèques testées. / The complete Gradle AAR differs as an archive, despite matches for the eight tested native libraries.
- Ces résultats viennent du rapport du développeur. Aucune attestation juridique, compilation reproductible ni démonstration fonctionnelle de remplacement d'une bibliothèque modifiée n'est revendiquée. / These results come from the developer's report. No legal certification, reproducible-build proof, or operational demonstration of modified-library replacement is claimed.

## 4. Droits des utilisateurs / User rights

Les bibliothèques FFmpeg couvertes par la LGPL conservent leurs licences et droits. DupliClean **n'interdit pas** la modification de ces bibliothèques ni l'ingénierie inverse nécessaire au débogage de modifications des parties LGPL, dans les limites et aux conditions prévues par les licences applicables. Cela ne signifie pas que le code propriétaire de DupliClean est licencié sous LGPL.

The LGPL-covered FFmpeg libraries retain their respective licenses and user rights. DupliClean **does not prohibit** modification of those libraries or reverse engineering for debugging modifications to LGPL-covered components as permitted by the applicable licenses. This does not license DupliClean's proprietary code under the LGPL.

**Remplacement / Replacement** : les fichiers FFmpeg sont des bibliothèques Android partagées `.so`, mais leur remplacement réel dans une application installable (reconditionnement d'APK et nouvelle signature, compatibilité d'interface, comportement avec une version modifiée, contraintes Play App Signing) **n'a pas encore été démontré**. L'utilisateur peut demander les instructions de reconstruction, les sources correspondantes et les éléments nécessaires à l'exercice de ses droits ; toute demande sera examinée selon les obligations applicables.

The FFmpeg files are Android shared `.so` libraries, but actual replacement in an installable application (APK repackaging and re-signing, interface compatibility, behavior with a modified version, Play App Signing constraints) **has not yet been demonstrated**. Users may request corresponding sources, rebuild instructions and materials necessary to exercise their rights; requests will be addressed under applicable licenses.

## 5. Travaux non encore démontrés / Outstanding verification

- Conserver ou mettre à disposition durablement un jeu **complet et correspondant** des sources FFmpegKit/FFmpeg (y compris les modifications appliquées par les scripts), les outils et paramètres de construction et les avis des dépendances tierces pertinentes. / Preserve or provide complete corresponding FFmpegKit/FFmpeg sources (including script-applied changes), toolchain/build parameters and any relevant dependency notices.
- Vérifier la configuration exacte non-GPL/non-`nonfree` de l'AAR distribué, idéalement à partir du journal/configuration native de la compilation et d'un inventaire des composants inclus. / Verify the actual non-GPL/non-`nonfree` build configuration and included components, preferably against the original native build log/configuration.
- Démontrer ou faire examiner spécifiquement la possibilité effective de remplacer les bibliothèques, les obligations d'informations d'installation et toute conséquence de la signature d'APK sous LGPL-3.0 §4 / GPL-3.0 §6. / Demonstrate or obtain a focused review of effective library replacement, installation-information obligations and APK-signing implications.
- Vérifier la visibilité des avis/licences dans les canaux de distribution applicables et l'interface utilisateur. / Check accessibility of notices/licenses in applicable distribution channels and the user interface.

**Cette page n'est pas une déclaration de conformité entièrement validée. / This page is not a statement of fully certified compliance.**

Références officielles / Official references: [GNU LGPL-3.0](https://www.gnu.org/licenses/lgpl-3.0.html), [FFmpeg Legal](https://ffmpeg.org/legal.html).

Pour les demandes de sources / For corresponding-source requests: [claudeaboki@gmail.com](mailto:claudeaboki@gmail.com).
