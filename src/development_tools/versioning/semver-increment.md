## 解析并递增版本字符串

<!--
> [development_tools/versioning/semver-increment.md](https://github.com/rust-lang-nursery/rust-cookbook/blob/master/src/development_tools/versioning/semver-increment.md)
> <br />
> commit b61c8e588ad8445de36cd5f28e99232b5f858a41 - 2020.06.01
-->

[![semver-badge]][semver] [![cat-config-badge]][cat-config]

使用 [`Version::parse`] 从字符串字面量构造语义化版本 [`semver::Version`]，然后逐个递增补丁（修订）版本号、副（次要）版本号和主版本号。

注意：根据[语义化版本控制规范][Semantic Versioning Specification]，增加副（次要）版本号时会将补丁（修订）版本号重置为 0，增加主版本号时会将副（次要）版本号和补丁（修订）版本号都重置为 0。

```rust,edition2018
use semver::{Version, Error, Prerelease,BuildMetadata};

fn main() -> Result<(), Error> {
    let mut parsed_version = Version::parse("0.2.6")?;

    assert_eq!(
        parsed_version,
        Version {
            major: 0,
            minor: 2,
            patch: 6,
            pre: Prerelease::EMPTY,
            build: BuildMetadata::EMPTY,
        }
    );


    increment_patch(&mut parsed_version);
    assert_eq!(parsed_version.to_string(), "0.2.7");
    println!("New patch release: v{}", parsed_version);

    increment_minor(&mut parsed_version);
    parsed_version.patch = 0;
    assert_eq!(parsed_version.to_string(), "0.3.0");
    println!("New minor release: v{}", parsed_version);

    increment_major(&mut parsed_version);
    assert_eq!(parsed_version.to_string(), "1.0.0");
    println!("New major release: v{}", parsed_version);

    Ok(())
}

// https://github.com/dtolnay/semver/issues/243
fn increment_patch(v: &mut Version) {
    v.patch += 1;
    v.pre = Prerelease::EMPTY;
    v.build = BuildMetadata::EMPTY;
}

fn increment_minor(v: &mut Version) {
    v.minor += 1;
    v.patch = 0;
    v.pre = Prerelease::EMPTY;
    v.build = BuildMetadata::EMPTY;
}

fn increment_major(v: &mut Version) {
    v.major += 1;
    v.minor = 0;
    v.patch = 0;
    v.pre = Prerelease::EMPTY;
    v.build = BuildMetadata::EMPTY;
}
```

[`semver::Version`]: https://docs.rs/semver/*/semver/struct.Version.html
[`Version::parse`]: https://docs.rs/semver/*/semver/struct.Version.html#method.parse

[Semantic Versioning Specification]: http://semver.org/
