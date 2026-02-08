# Running Disco tools (Disruptor, Distributor, Duplicator, Disrespector)

The built distribution contains launchers in `bin/` and sample configuration in `etc/`. These instructions assume you have built a sandbox or release and are running inside `codebase/dist/disco-x.x.x/` (or an extracted release ZIP). The same commands also work in the repo by using the scripts from `codebase/resources/dist/common/bin`, but the standard workflow is to run from the built distribution. 【F:README.md†L34-L69】

> **Windows users:** use the `.bat` versions of each launcher. 【F:README.md†L34-L69】

---

## 1) Disruptor — traffic generator for load testing (main focus)

**Purpose:** Generates realistic DIS network traffic for load testing and verification. 【F:codebase/resources/dist/common/documentation/Disruptor.md†L1-L18】

### Start Disruptor

From your distribution root:

```bash
bin/disruptor.sh --config-file etc/disruptor.config --plan-file etc/disruptor.plan
```

The launcher accepts optional `--config-file` and `--plan-file` arguments. If you omit them, defaults are used. 【F:codebase/resources/dist/common/documentation/Disruptor.md†L29-L38】【F:codebase/src/java/disco/org/openlvc/disruptor/Configuration.java†L43-L73】【F:codebase/src/java/disco/org/openlvc/disruptor/Configuration.java†L320-L361】

### Key configuration files

- **`etc/disruptor.config`** — DIS network + logging configuration (annotated). 【F:codebase/resources/dist/common/documentation/Disruptor.md†L69-L75】【F:codebase/resources/dist/common/etc/disruptor.config†L1-L59】
- **`etc/disruptor.plan`** — JSON plan defining entities and movement paths. 【F:codebase/resources/dist/common/documentation/Disruptor.md†L69-L111】

### Disruptor configuration parameters

You can set these either in `etc/disruptor.config` or via CLI args (see below):

- **Logging**
  - `disruptor.loglevel`, `disruptor.logfile` — Disruptor app logging. 【F:codebase/resources/dist/common/etc/disruptor.config†L13-L24】
  - `disruptor.disco.loglevel` — Disco library log level. 【F:codebase/resources/dist/common/etc/disruptor.config†L26-L31】
- **DIS networking**
  - `disruptor.dis.address` — `BROADCAST` or multicast address. 【F:codebase/resources/dist/common/etc/disruptor.config†L36-L45】
  - `disruptor.dis.port` — UDP port (default 3000). 【F:codebase/resources/dist/common/etc/disruptor.config†L47-L52】
  - `disruptor.dis.nic` — NIC selection (e.g., `SITE_LOCAL`, `GLOBAL`, IP address). 【F:codebase/resources/dist/common/etc/disruptor.config†L54-L66】
- **Simulation controls** (typically set via CLI; available as properties):
  - `disruptor.loops`, `disruptor.tickInterval`, `disruptor.dis.simaddress`. 【F:codebase/src/java/disco/org/openlvc/disruptor/Configuration.java†L43-L70】

### Disruptor plan (`etc/disruptor.plan`) overview

The plan file has three top-level sections: `entities`, `locations`, and `paths`. 【F:codebase/resources/dist/common/documentation/Disruptor.md†L87-L111】

- **`entities`** defines named DIS enumerations for entity types. You can use arrays or delimited strings for enumerations. 【F:codebase/resources/dist/common/documentation/Disruptor.md†L113-L153】
- **`locations`** defines named lat/lon/alt locations used by paths. 【F:codebase/resources/dist/common/documentation/Disruptor.md†L167-L206】
- **`paths`** defines motion paths with `line`, `circle`, and `polygon` types. Each path sets `entityType`, `count`, `spacing`, and `path`. 【F:codebase/resources/dist/common/documentation/Disruptor.md†L214-L251】

### Disruptor CLI options (most common)

From the built-in help text:

- `--config-file` / `--plan-file` — override file locations. 【F:codebase/src/java/disco/org/openlvc/disruptor/Configuration.java†L320-L343】
- `--loops`, `--tick-interval`, `--simulation-address` — simulation control. 【F:codebase/src/java/disco/org/openlvc/disruptor/Configuration.java†L320-L343】
- `--dis-address`, `--dis-port`, `--dis-interface`, `--dis-exercise-id` — DIS network settings. 【F:codebase/src/java/disco/org/openlvc/disruptor/Configuration.java†L320-L361】
- `--log-level`, `--pdu-sender`, `--pdu-receiver`. 【F:codebase/src/java/disco/org/openlvc/disruptor/Configuration.java†L320-L361】

---

## 2) Distributor — DIS network bridging, site-to-site connections, filtering

**Purpose:** Bridges and filters DIS networks via configurable links (local DIS, WAN relay, etc.). 【F:codebase/resources/dist/common/documentation/Distributor.md†L1-L20】【F:codebase/resources/dist/common/documentation/Distributor.md†L47-L74】

### Start Distributor

```bash
bin/distributor.sh --config-file etc/distributor.config
```

The default config is used if you omit `--config-file`. 【F:codebase/resources/dist/common/documentation/Distributor.md†L76-L89】【F:codebase/src/java/disco/org/openlvc/distributor/configuration/Configuration.java†L248-L257】

### Key configuration file

- **`etc/distributor.config`** — annotated default config with link modes, logging, and filtering. 【F:codebase/resources/dist/common/documentation/Distributor.md†L111-L117】【F:codebase/resources/dist/common/etc/distributor.config†L1-L140】

### Core concepts & parameters

- **Links** are defined via `distributor.links` and then configured under `distributor.<link>.…`. 【F:codebase/resources/dist/common/documentation/Distributor.md†L118-L145】【F:codebase/resources/dist/common/etc/distributor.config†L57-L86】
- **Link modes** include `dis`, `wan`, `relay`, `pulse`, and `logging`. Each mode has its own settings in the config file. 【F:codebase/resources/dist/common/documentation/Distributor.md†L47-L74】【F:codebase/resources/dist/common/etc/distributor.config†L28-L55】
- **Filters** can be applied per link with `distributor.<link>.filter.recv` and `distributor.<link>.filter.send`. 【F:codebase/resources/dist/common/documentation/Distributor.md†L194-L214】【F:codebase/resources/dist/common/etc/distributor.config†L93-L107】

### Distributor CLI options

From the built-in help text:

- `--config-file` — config path (default `etc/distributor.config`). 【F:codebase/src/java/disco/org/openlvc/distributor/configuration/Configuration.java†L248-L257】
- `--log-level` — logging threshold. 【F:codebase/src/java/disco/org/openlvc/distributor/configuration/Configuration.java†L248-L257】
- `--status-interval` — periodic link status logging (seconds). 【F:codebase/src/java/disco/org/openlvc/distributor/configuration/Configuration.java†L248-L257】

---

## 3) Duplicator — record and replay DIS traffic

**Purpose:** Record DIS traffic to a session file and replay it later (including pcap/pcapng or zipped session files for replay). 【F:codebase/src/java/disco/org/openlvc/duplicator/Configuration.java†L481-L520】

### Start Duplicator (recording)

```bash
bin/duplicator.sh --record --file duplicator.session
```

`--record` is the default mode. 【F:codebase/src/java/disco/org/openlvc/duplicator/Configuration.java†L481-L499】

### Start Duplicator (replay)

```bash
bin/duplicator.sh --replay --file duplicator.session
```

Replay supports Duplicator session files and pcap/pcapng (including zipped sessions). 【F:codebase/src/java/disco/org/openlvc/duplicator/Configuration.java†L481-L499】

### Key parameters (CLI)

Duplicator is primarily configured by CLI options. From the built-in help text:

- `--record`, `--replay`, `--file` — select mode and session file. 【F:codebase/src/java/disco/org/openlvc/duplicator/Configuration.java†L481-L499】
- `--dis-interface`, `--dis-address`, `--dis-port`, `--dis-exercise-id` — DIS network settings. 【F:codebase/src/java/disco/org/openlvc/duplicator/Configuration.java†L485-L500】
- `--replay-realtime`, `--replay-fast`, `--loop` — replay timing/looping. 【F:codebase/src/java/disco/org/openlvc/duplicator/Configuration.java†L487-L505】
- `--log-level`, `--status-interval`, `--pdu-sender`, `--pdu-receiver`. 【F:codebase/src/java/disco/org/openlvc/duplicator/Configuration.java†L488-L503】
- **HLA options** (`--hla`, `--hla-federate`, `--hla-federation`, `--hla-rti-provider`, `--hla-rti-installdir`, `--hla-create-federation`, `--hla-rti-settings`) to run against HLA instead of DIS. 【F:codebase/src/java/disco/org/openlvc/duplicator/Configuration.java†L503-L520】

---

## 4) Disrespector — DIS ↔ HLA/RPR bridge

**Purpose:** Bridges between DIS and HLA/RPR networks, with configurable DIS and HLA settings. The default config file is `etc/disrespector.config`. 【F:codebase/src/java/disco/org/openlvc/disrespector/Configuration.java†L55-L92】

### Start Disrespector

```bash
bin/disrespector.sh --config-file etc/disrespector.config
```

`--config-file` is optional and defaults to `etc/disrespector.config`. 【F:codebase/src/java/disco/org/openlvc/disrespector/Configuration.java†L77-L92】【F:codebase/src/java/disco/org/openlvc/disrespector/Configuration.java†L487-L515】

### Key configuration file

- **`etc/disrespector.config`** — DIS and HLA settings (annotated). 【F:codebase/resources/dist/common/etc/disrespector.config†L1-L46】

Key parameters from the config:

- **DIS**: `disrespector.dis.address`, `disrespector.dis.port`, `disrespector.dis.nic`, `disrespector.dis.exerciseId`, plus DIS log settings. 【F:codebase/resources/dist/common/etc/disrespector.config†L14-L44】
- **HLA**: `disrespector.hla.rti.provider`, `disrespector.hla.rti.installdir`, `disrespector.hla.rti.localSettings`, federation/federate names, and federation creation flags. 【F:codebase/resources/dist/common/etc/disrespector.config†L49-L66】

### Disrespector CLI options

From the built-in help text:

- `--config-file`, `--log-level`, `--log-file` (applies to both DIS/HLA). 【F:codebase/src/java/disco/org/openlvc/disrespector/Configuration.java†L487-L506】
- **DIS options:** `--dis-exercise-id`, `--dis-address`, `--dis-port`, `--dis-interface`, `--dis-log-level`, `--dis-log-file`. 【F:codebase/src/java/disco/org/openlvc/disrespector/Configuration.java†L501-L510】
- **HLA options:** `--hla-rti-provider`, `--hla-rti-dir`, `--hla-local-settings`, `--hla-federation`, `--hla-federate`, `--hla-log-level`, `--hla-log-file`. 【F:codebase/src/java/disco/org/openlvc/disrespector/Configuration.java†L511-L520】
