# DupliClean 1.2.3 (20) — FFmpegKit / FFmpeg: sources et reconstruction / source and rebuild information

**État / Status : ATTENTION — décalage entre les scripts de provenance et la version native observée; conformité LGPL complète NON certifiée / WARNING — mismatch between published source scripts and observed native version; full LGPL compliance NOT certified.**

> **DUP071-28 / rectification du 22 novembre 2026** : les bibliothèques `libavcodec.so` et `libavutil.so` de l’APK expérimental contiennent **`FFmpeg version n8.1.2`** ; le fichier `libavcodec.so` est octet pour octet identique à la bibliothèque du fournisseur AAR. En revanche, `scripts/source.sh` au commit FFmpegKit `d28006b5...` désigne `n7.1.5`. **Ce script ne désigne donc pas les sources correspondant à l’AAR effectivement distribué.** Source amont à examiner : [`arthenica/FFmpeg`, tag `n8.1.2`, commit `38b88335f99e76ed89ff3c93f877fdefce736c13`](https://github.com/arthenica/FFmpeg/tree/38b88335f99e76ed89ff3c93f877fdefce736c13). De plus, dans `libavutil/log.c` v8.1.2, la variable est déclarée `static atomic_int av_log_level`, alors que le script FFmpegKit au commit indiqué cherche `static int av_log_level`. **Il faut retrouver la révision effective des scripts et leurs modifications avant de déclarer ces sources exactes.**
>
> **English:** Actual shipped `libavcodec.so` contains `FFmpeg version n8.1.2`, but the pinned FFmpegKit script references `n7.1.5`. Those scripts do not identify the shipped FFmpeg source. FFmpeg 8.1.2 upstream commit `38b88335...` is a candidate baseline only. The `av_log_level` declaration also differs; actual build-time patches remain unverified.


Cette page complète les [notices de licences tierces](../third-party-licenses.md) de DupliClean. Elle ne publie pas le code propriétaire de l'application.

This page supplements DupliClean's [third-party license notices](../third-party-licenses.md). It does not publish proprietary application source code.

## 1. Composants identifiés / Identified components

- **FFmpegKit Android (min)** : `io.github.maitrungduc1410:ffmpeg-kit-min:8.1.2` ; projet et scripts de construction / project and build scripts: [commit `d28006b5b718a324b89962e70feedc4512c5942a`](https://github.com/maitrungduc1410/ffmpeg-kit/tree/d28006b5b718a324b89962e70feedc4512c5942a), tag `android-8.1.2`.
- **Archive binaire du fournisseur / Distributor binary AAR** : [`ffmpeg-kit-min-8.1.2.aar`](https://github.com/maitrungduc1410/ffmpeg-kit/releases/download/android-8.1.2/ffmpeg-kit-min-8.1.2.aar), 15 446 429 octets / bytes, SHA-256 `53e3f17a8971eabc03910db1e6d59366ca1961031f98ef3f84ec925cb1fe478c`.
- **Sources FFmpeg référencées par le script historique, NON correspondantes aux binaires distribués / FFmpeg referenced by the historical script, NOT matching shipped binaries** : [`arthenica/FFmpeg`, tag `n7.1.5`](https://github.com/arthenica/FFmpeg/tree/n7.1.5), tel que spécifié dans [`scripts/source.sh`](https://github.com/maitrungduc1410/ffmpeg-kit/blob/d28006b5b718a324b89962e70feedc4512c5942a/scripts/source.sh).
- **Scripts et modifications de construction / Build scripts and source modifications** : [`android.sh`](https://github.com/maitrungduc1410/ffmpeg-kit/blob/d28006b5b718a324b89962e70feedc4512c5942a/android.sh), [`scripts/android/ffmpeg.sh`](https://github.com/maitrungduc1410/ffmpeg-kit/blob/d28006b5b718a324b89962e70feedc4512c5942a/scripts/android/ffmpeg.sh), [workflow Android](https://github.com/maitrungduc1410/ffmpeg-kit/blob/d28006b5b718a324b89962e70feedc4512c5942a/.github/workflows/android-build-scripts.yml), [`tools/protocols/`](https://github.com/maitrungduc1410/ffmpeg-kit/tree/d28006b5b718a324b89962e70feedc4512c5942a/tools/protocols).
- **JCodec** : `org.jcodec:jcodec:0.2.5`, BSD-2-Clause ; voir les notices principales / see the main notices.

## 2. Configuration publiée par le fournisseur / Distributor-published build configuration

Au commit ci-dessus, le workflow Android **historique (non vérifié comme provenance de l'AAR 8.1.2)** définit NDK `r27c-linux`, le profil **min**, et les indicateurs communs `--enable-android-media-codec --enable-android-zlib --disable-arm-v7a --disable-x86 --disable-x86-64`. Le script FFmpeg déclare des bibliothèques partagées (`--disable-static --enable-shared`) et `--enable-version3`. Le fournisseur annonce une version LGPL-3.0 sans composants GPL pour cette release.

At the pinned commit, the Android workflow specifies NDK `r27c-linux`, the **min** variant, and common flags `--enable-android-media-codec --enable-android-zlib --disable-arm-v7a --disable-x86 --disable-x86-64`. The FFmpeg build script specifies shared libraries (`--disable-static --enable-shared`) and `--enable-version3`. The distributor describes this release as LGPL-3.0 without GPL-enabled components.

**Limite critique / Critical limitation** : le script historique sélectionne **FFmpeg 7.1.5**, alors que le binaire fournit **FFmpeg 8.1.2**. Ces scripts documentent une recette différente, et nous n'avons pas reproduit indépendamment la construction à partir de ces sources ni vérifié le `config.log` original du fournisseur. Une recette publiée et une archive binaire identifiée ne prouvent pas, à elles seules, l'intégralité des paramètres et modifications effectivement utilisés. / These scripts document a build recipe, but no independent rebuild or inspection of the distributor's original `config.log` has been completed.

### Contrôle direct de la configuration native / Direct native configuration inspection — DUP071-27

Le 22 novembre 2026, nous avons extrait en lecture seule la chaîne de configuration FFmpeg depuis les huit bibliothèques ARM64 d'un APK expérimental dérivé de l'AAB candidat. Les **huit chaînes sont identiques**, de **1 442 octets** et de SHA-256 `e84d0c7659f5b227b6aaf258750cf3171974324c78803b899676e1428807f89f`. Les bibliothèques non modifiées (dont `libavcodec.so`, SHA-256 `a01398fde2967e09ba8c01a578094b06fc72273f7c82a02f13e1bf2896afae83`) correspondent aux fichiers du fournisseur selon l'audit DUP071-21. Dans cet APK expérimental, **`libavutil.so` seule porte une section ELF de diagnostic non chargée** ; sa chaîne de configuration est inchangée.

Les paramètres enregistrés comprennent **Android ARM64, NDK r27c**, `--enable-version3`, `--disable-static`, `--enable-shared`, `--enable-zlib`, `--enable-mediacodec`, `--disable-autodetect`. **`--enable-gpl` et `--enable-nonfree` ne figurent pas dans la chaîne.** Ce contrôle porte sur les **binaires** : il ne démontre ni la révision source exacte qui a produit l'AAR, ni l'absence de toute dépendance incompatible, ni une compilation reproductible.

On 22 November 2026, the recorded FFmpeg configuration string was extracted from eight ARM64 shared libraries in an experimental APK derived from the candidate AAB. All eight strings match (1,442 bytes; SHA-256 above), and the unmodified libraries match the supplier's binaries. The recorded options include shared linking and NDK r27c and do not contain `--enable-gpl` or `--enable-nonfree`. This provides **binary-level evidence**, not independently reproduced corresponding sources or an exhaustive third-party license audit.

**Recette d'interface / UI validation:** The developer has also confirmed successful operation on Samsung of the newly added *Third-party licenses* page in DupliCleanTest, with offline texts, supported languages and scans. This TEST validation requires a fresh production AAB before release.

## 3. Traçabilité DupliClean / DupliClean traceability

Selon le rapport d'audit local `DUP071-21` (22 novembre 2026, 18:17) / According to the local `DUP071-21` audit report (2026-11-22, 18:17):

- AAB candidat DupliClean `1.2.3 (20)` : **93 449 384 octets**, SHA-256 `e5cc63099d198e364ad825e96b99fc8b6838b926345507986b4b56b35fc18924`.
- Les huit fichiers `arm64-v8a/libavcodec.so`, `libavdevice.so`, `libavfilter.so`, `libavformat.so`, `libavutil.so`, `libffmpegkit.so`, `libswresample.so`, `libswscale.so` ont chacun le **même SHA-256 dans l'AAB, le cache Gradle et l'AAR du fournisseur**. / These eight ARM64 files had matching individual SHA-256 values in the AAB, Gradle cache and distributor AAR.
- Le fichier AAR du cache Gradle est différent au niveau **archive** (15 438 247 octets; SHA-256 `a653e08aec52d6183755623aa451b891663a2bcdd4d50faaf5dd2c9bc721827b`), malgré l'identité des huit bibliothèques testées. / The complete Gradle AAR differs as an archive, despite matches for the eight tested native libraries.
- Ces résultats viennent du rapport du développeur. Aucune attestation juridique, compilation reproductible ni démonstration fonctionnelle de remplacement d'une bibliothèque modifiée n'est revendiquée. / These results come from the developer's report. No legal certification, reproducible-build proof, or operational demonstration of modified-library replacement is claimed.

## 4. Droits des utilisateurs / User rights

Les bibliothèques FFmpeg couvertes par la LGPL conservent leurs licences et droits. DupliClean **n'interdit pas** la modification de ces bibliothèques ni l'ingénierie inverse nécessaire au débogage de modifications des parties LGPL, dans les limites et aux conditions prévues par les licences applicables. Cela ne signifie pas que le code propriétaire de DupliClean est licencié sous LGPL.

The LGPL-covered FFmpeg libraries retain their respective licenses and user rights. DupliClean **does not prohibit** modification of those libraries or reverse engineering for debugging modifications to LGPL-covered components as permitted by the applicable licenses. This does not license DupliClean's proprietary code under the LGPL.

**Remplacement / Replacement** : les fichiers FFmpeg sont des bibliothèques Android partagées `.so`. Un test DUP071-22D a permis de remplacer un `.so` par une version dotée d'une section ELF de diagnostic **non chargée**, puis de reconditionner et signer un APK avec une clé Debug indépendante ; le développeur a confirmé que l'APK s'installe et que les scans fonctionnent sur Samsung. **Un remplacement fonctionnel issu de sources recompilées n'a pas été démontré**. L'utilisateur peut demander les instructions de reconstruction, les sources correspondantes et les éléments nécessaires à l'exercice de ses droits ; toute demande sera examinée selon les obligations applicables.

FFmpeg uses Android shared `.so` libraries. In DUP071-22D a library containing an additional **non-loaded ELF diagnostic section** was repackaged and the APK was signed with a separate Debug key; the developer confirmed installation and functioning scans on Samsung. **Replacement with a functionally modified library rebuilt from source has not been demonstrated.** Users may request corresponding sources, rebuild instructions and materials necessary to exercise their rights; requests will be addressed under applicable licenses.

## 5. Travaux non encore démontrés / Outstanding verification

- Conserver ou mettre à disposition durablement un jeu **complet et correspondant** des sources FFmpegKit/FFmpeg (y compris les modifications appliquées par les scripts), les outils et paramètres de construction et les avis des dépendances tierces pertinentes. / Preserve or provide complete corresponding FFmpegKit/FFmpeg sources (including script-applied changes), toolchain/build parameters and any relevant dependency notices.
- Vérifier la configuration exacte non-GPL/non-`nonfree` de l'AAR distribué, idéalement à partir du journal/configuration native de la compilation et d'un inventaire des composants inclus. / Verify the actual non-GPL/non-`nonfree` build configuration and included components, preferably against the original native build log/configuration.
- Démontrer ou faire examiner spécifiquement la possibilité effective de remplacer les bibliothèques, les obligations d'informations d'installation et toute conséquence de la signature d'APK sous LGPL-3.0 §4 / GPL-3.0 §6. / Demonstrate or obtain a focused review of effective library replacement, installation-information obligations and APK-signing implications.
- Vérifier la visibilité des avis/licences dans les canaux de distribution applicables et l'interface utilisateur. / Check accessibility of notices/licenses in applicable distribution channels and the user interface.

**Cette page n'est pas une déclaration de conformité entièrement validée. / This page is not a statement of fully certified compliance.**

Références officielles / Official references: [GNU LGPL-3.0](https://www.gnu.org/licenses/lgpl-3.0.html), [FFmpeg Legal](https://ffmpeg.org/legal.html).

Pour les demandes de sources / For corresponding-source requests: [claudeaboki@gmail.com](mailto:claudeaboki@gmail.com).
