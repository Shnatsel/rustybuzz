# rustybuzz
![Build Status](https://github.com/RazrFalcon/rustybuzz/workflows/Rust/badge.svg)
[![Crates.io](https://img.shields.io/crates/v/rustybuzz.svg)](https://crates.io/crates/rustybuzz)
[![Documentation](https://docs.rs/rustybuzz/badge.svg)](https://docs.rs/rustybuzz)

`rustybuzz` is a complete [harfbuzz](https://github.com/harfbuzz/harfbuzz)'s
shaping algorithm port to Rust.

Matches `harfbuzz` v2.7.0

## Why?

Because you can add `rustybuzz = "*"` to your project and it just works.
No need for a C++ compiler. No need to configure anything. No need to link to system libraries.

## Conformance

rustybuzz passes 98% of harfbuzz tests (1764 to be more precise).
So it's mostly identical, but there are still some tiny edge-cases which
are not implemented yet or cannot be implemented at all.

## Major changes

- Subsetting removed.
- TrueType parsing has been implemented from scratch, mostly on the
  [ttf_parser](https://github.com/RazrFalcon/ttf_parser) side.
  And while the parsing algorithm is very different, it's not better or worse, just different.
- Malformed fonts will cause an error. HarfBuzz uses fallback/dummy shaper in this case.
- Most of the TrueType and Unicode handling code was moved into separate crates.
- rustybuzz doesn't interact with any system libraries and must produce exactly the same
  results on all OS'es and targets.
- `mort` table is not supported, since it's deprecated by Apple.
- No Arabic fallback shaper, since it requires subsetting.
- No `graphite` library support.

## Performance

At the moment, performance isn't that great. We're 1.5-2x slower than harfbuzz.
Also, rustybuzz doesn't support shaping plan caching at the moment.

See [benches/README.md](./benches/README.md) for details.

## Notes about the port

rustybuzz is not a faithful port.

harfbuzz can roughly be split into 6 parts: shaping, subsetting, TrueType parsing,
Unicode routines, custom containers and utilities (harfbuzz doesn't use C++ std)
and glue for system/3rd party libraries.
While rustybuzz contains only shaping and some TrueType parsing.
Most of the TrueType parsing was moved to the [ttf_parser](https://github.com/RazrFalcon/ttf_parser).
Subseting was removed. Unicode code mostly moved to external crates.
We don't need custom containers because Rust's std is good enough.
And we do not use any non Rust libraries, so no glue code either.

In the end, we still have around 23 KLOC. While harfbuzz is around 80 KLOC.

## Lines of code

As mentioned above, rustybuzz has around 23 KLOC. But this is not strictly true,
because there are a lot of auto-generated data tables.

You can find the "real" code size using:

```sh
tokei --exclude unicode_norm.rs --exclude complex/vowel_constraints.rs \
      --exclude '*_machine.rs' --exclude '*_table.rs'
```

Which gives us around 16 KLOC, which is still a lot.

## Future work

Since the port is finished, there is not much to do other than syncing it with
a new harfbuzz releases.
But there are still a lot of room for performance optimizations and refactoring.

Also, despite the fact that harfbuzz has a vast test suite, there are still a lot of
things left to test.

## License

`rustybuzz` is licensed under the **MIT**.

`harfbuzz` is [licensed](https://github.com/harfbuzz/harfbuzz/blob/master/COPYING) under the **Old MIT**
