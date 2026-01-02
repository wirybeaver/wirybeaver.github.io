---
layout:     post
title:      Pinot Note
excerpt:    ""
author:     "Xuanyi"
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - Data
---

# Index

Pinot Index [UML](https://www.planttext.com/?text=fLTBRzj64BxlhnYa5mcmsCXjBx9X99PX942C15QX7eh2M953YJ39PNUNifNG_xsp3qfyIMhHayLvFxwP7NzGcajTPYbBSeqovY72vpp2rzzt1S8FziROuJEuSRovHAw5PBdG22A7DCcVPn0QAZR58_6IrpgFo2EKzpmanTrPpR8WlOTIyrMAbZoiqGtQEN6oCbblD8YunScuV4O9UxKhbcH52lYgCOyKw0qoLM2On4a8qgaG0up1fhDx8L-uZ1gMgLHWDUwIK7-Er2W8nfwov6c9Qh6i7fQMIYKDKwuKBBcytMcSRyhykMzn-Qkr-HGQjfh1Di4NaqwMyGA-9HJI7TVyjenZ2XB0ASq5nVYgVNtyrwZXSowoGg9I6BKLVafDkLOKgVCQORYNT3vcS9T83BK9sNjzOsTJ25ACzSqnjqUwEb3UYMtdf6gXwRpxILcGJfzr13aUdhO4JUyki0PbjC1rXhbs9I6LmD4lm7UOhvIMP0tkCEPbgl-loNFA7K3KSTZwcXOOvFXGPhDt_ytvEwQHfeGq84Ez4T6CITIbp0VYe411uTfz1MlKbaaDCD1tKUq7oY6otvnwITIbkwucanDYRA21LiNhjmJWEZYtZvZBoiy_5OvsHEPM4IqEiuk6LaAtWBRXdQv0fnxK85S3h_n5X3ozN-njT1RP4Qd6xCw0fYn67MxScbYvETs3SIxICijj20SdFeGaNwTud_XzssKRnZUKRLvlQpX_jAo-94fV7xBVhuoR6fFxf0ODKnvcI-_BhbpcR2X-3uyXCvtrifrMbt32nj4ITCpymevrGf2LmHhtqE4S7cbxJpgWrx16SpiOqsKBIeWfRg9h0RpkUmEUpRVKzef7qMZBqmk8x8D5VqNuEHeVEAfcgJtlRS1YSoxRxzfYEI3YSKBiZrBotOBIGqF9CAD47tZEr_PXSPQyWQzn_0bvSTlI6aBS0MHEtu-TbInwJsQO2eMZyOzSYEAj1qmlPYe1dDnHMJ22M8hCd0cm4bgBZ8auLgYR4jG0mzUYO47W-LEemFswh8WO11KtUHYo_V4U6Zl5fqwTVMWMvLyFcw_18RzQCl24VguzPsmZqWXMEq1kTabd3Fs2D8XJzYJXhwFtmM_h2zgPVuyPfrJi-Y6KHfZHPoEx8KoOPxrrx0qA6Qc1b_masYODqosadaJMH9p4CEltPd9EnIFHo9roFM3QpBx7OcSFcOEGYlkpaC3Jb9R8m4NiJtM3cCx4Zrb_qiSzPBJkVT_tpdDunQHuQKv0ahk50KAQ8-3i1LsR7w2v1hQawlTgwUlzA5wwcSAJ3UmFcMYxJ8xAzZ3LSNBildIMwRvipQGxrGVkvAVwbav4FZyc4z7ipZSypp6TsUVCVx0dKUgYr3Duf6QBx6COaYKZOFQnxPRRHQPTvm9cPaCij2n3kj-eNngCE5s8M-F8nw_i42GvHKql8j34omILkUmcbKg-uxGofDtKVbow3xlNsZdehBY2JRbsqKnkcjo0O3uoZmrOdzFA_h56zhIPNvbJebRkPrvx9ehzbU-hO_JeEkN6YVbTcKm_sHMXPd-EfefESGFA2_XgJOrDkPoZ8CZ-iYfKgcIRH2LFRSWACh6jTtuZ-0QWZo9BJCxtuWFPCVzv_mC0)

<img src="https://i.imgur.com/0doVwZd.png" width="60%" border="5">

The key points of Pinot Index System
- IndexCreator build the immutable index; IndexReaderFactory create the reader for immutable index
- MutableXXXIndex inherit MutableIndex (write api) and IndexReader (read api)
- ForwardIndex: Given a docID (rowID), return the raw value or the dictionaryID of the raw value depending on the encoding method you set up for that column. Each columns must have a ForwardIndex
- Pinot's InvertedIndex (column value to the bitmap), JsonIndex, TextIndex, GeoIndex are the general concept of index in database world, i.e. given a column value (raw value or dictionaryID of raw value), return the docID.
- Pinot create mutable / immutable index per column per segment.

# Pinot Segment Commit Protocol
This pauseless ingsetion [design](https://docs.google.com/document/d/1d-xttk7sXFIOqfyZvYw5W_KeGS6Ztmi8eYevBcCrT_c/edit?usp=sharing) provides a clear sequence diagram of the interaction among pinot controller / servers, segments' IdeaStatate and segment metadata.
