# Building Disco (from source)

This project uses an Ant-based build system with a wrapper script checked into the repo. The core workflow is to build a “sandbox” distribution that contains the runnable `bin/`, `etc/`, and `lib/` directories. The `README.md` in the repo describes the standard Ant targets and the `build.xml` file defines them. The build output is written under `codebase/dist/` by default. 【F:README.md†L70-L120】【F:codebase/build.xml†L36-L88】

## Prerequisites

1. **JDK 8u60+** on your system path. The project documentation calls out Java 8u60+ as the supported baseline. 【F:README.md†L76-L83】
2. **Ant** is already bundled via the `codebase/ant` and `codebase/ant.bat` wrapper scripts, so you do not need a system-wide Ant installation. The provided `build.xml` is the project’s entry point. 【F:codebase/build.xml†L1-L88】
3. **Optional for releases:** If you plan to generate a release or installer, copy `codebase/build.properties` to `codebase/local.properties` (git-ignored) and set any machine-specific paths as described in the README. 【F:README.md†L80-L87】

## Step-by-step build (recommended: sandbox)

> The sandbox build produces an “exploded” install you can run locally.

1. **Go to the build directory**:
   ```bash
   cd /workspace/disco/codebase
   ```
2. **(Optional) Create `local.properties` for release builds**:
   ```bash
   cp build.properties local.properties
   # then edit local.properties as needed for your environment
   ```
   This step is only needed for release/installer builds. 【F:README.md†L80-L87】
3. **Build the sandbox distribution**:
   ```bash
   ./ant sandbox
   ```
   This runs the default `sandbox` target, which creates a runnable distribution under `codebase/dist/disco-x.x.x/`. 【F:README.md†L88-L118】【F:codebase/build.xml†L36-L88】

## Other useful build targets

You can run these from `codebase/` as needed: 【F:README.md†L88-L118】【F:codebase/build.xml†L36-L88】

```bash
./ant compile      # compile production code
./ant test         # run tests
./ant clean        # remove build artifacts
./ant release      # clean + test + full release package
./ant release.thin # release package without tests
./ant installer    # installer package from sandbox
./ant -projecthelp # list targets
```

## Build output layout (what you get)

After `./ant sandbox`, your distribution will look like this under `codebase/dist/disco-x.x.x/`:

- `bin/` — launch scripts for Disruptor, Distributor, Duplicator, Disrespector, etc.
- `etc/` — sample configuration files
- `lib/` — JARs
- `documentation/` — app-specific docs bundled with the distribution

This layout is shown in the project README. 【F:README.md†L34-L69】
