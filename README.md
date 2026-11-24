# AVTD — Audio-Visual Timeline Description

**Version 1.0.0**

AVTD is a standard tabular (CSV) format for describing a music or audio
timeline: absolute and musical time for every event, plus semantic context
(tags, natural-language captions) that both LLMs and plain scripts can parse
deterministically.

It is designed for the case where **the timeline is authored up front** — by an
LLM, a Music AI agent, a DAW converter or a human analyst — and then consumed by
a renderer or video tool as finalized data, instead of being analyzed with
FFT/beat detection at render time.

## Files

| Path | Description |
| :--- | :--- |
| [`SPECIFICATION.md`](SPECIFICATION.md) | The full AVTD 1.0.0 specification. |
| [`examples/example.csv`](examples/example.csv) | A short annotated timeline (sections, vocal phrases, kick/snare hits). |
| [`CHANGELOG.md`](CHANGELOG.md) | Version history. |

## Quick example

```csv
time_sec,time_bar,duration,event_type,target_part,tags,caption
00:01.850,1.4.000,3.150,section,Structure,drop|trap_beat|high_energy,Drop section start. Full trap drum pattern enters.
00:01.850,1.4.000,0.250,hit,Kick,heavy_kick|downbeat|sync_bass,Main drop downbeat kick, tightly locked with sub-bass.
```

- `time_sec` — absolute time, `MM:SS.mmm`.
- `time_bar` — musical time, `Bar.Beat.Tick`.
- `event_type` — one of `section`, `phrase`, `hit`, `sustain`.
- `target_part` — `Structure`, `Kick`, `Snare`, `Vocal`, `Synth`, … (see spec §3.4).
- `tags` — `lowercase_snake_case` items joined by `|`.
- `caption` — free-form text; quote it when it contains commas.

## Validation

A reference parser and validator are maintained in the
[`film`](https://github.com/1l0/film) project
(`packages/film_core/lib/src/avtd.dart`, CLI `film avtd validate <file.csv>`).

## Versioning

The spec is versioned in the document itself (`**Version:** 1.0.0`) and this
repository is tagged with matching `vX.Y.Z` tags. Backwards-incompatible
changes bump the major version.

## License

MIT — see [LICENSE](LICENSE).
