---
html: commandline-usage.html
name: Commandline Usage
parent: infrastructure.html
seo:
    description: Commandline usage options for the rippled server.
curated_anchors:
  - name: Available Modes
    anchor: "#available-modes"
  - name: Daemon Mode Options
    anchor: "#daemon-mode-options"
  - name: Stand-Alone Mode Options
    anchor: "#stand-alone-mode-options"
  - name: Client Mode Options
    anchor: "#client-mode-options"
  - name: Unit Tests
    anchor: "#unit-tests"
labels:
  - Core Server
---
# Commandline Usage

The `rippled` executable usually runs as a daemon that powers the XRP Ledger, although it can also run in other modes. This page describes all the options you can pass to `rippled` when running it from the command line.

## Available Modes

- **Daemon Mode** - The default. Connect to the XRP Ledger to process transactions and build a ledger database.
- **Stand-Alone Mode** - Use the `-a` or `--standalone` option. Like daemon mode, except it does not connect to other servers. You can use this mode to test transaction processing or other features.
- **Client Mode** - Specify an API method name to connect to another `rippled` server as a JSON-RPC client, then exit. You can use this to look up server status and ledger data if the executable is already running in another process.
- **Other Usage** - Each of the following commands causes the `rippled` executable to print some information, then exit:
    - **Help** - Use `-h` or `--help` to print a usage statement.
    - **Unit Tests** - Use `-u` or `--unittest` to run unit tests and print a summary of results. This can be helpful to confirm that you have compiled `rippled` successfully.
    - **Version statement** - Use `--version` to have `rippled` print its version number, Git commit hash, and Git build branch.

## Generic Options

These options apply to most modes:

| Option          | Description                                                |
|:----------------|:-----------------------------------------------------------|
| `--conf {FILE}` | Use `{FILE}` as the config file instead of looking for config files in the default locations. If not specified, `rippled` first checks the local working directory for a `rippled.cfg` file. On Linux, if that file is not found, `rippled` next checks for `$XDG_CONFIG_HOME/ripple/ripple.cfg`. (Typically, `$XDG_CONFIG_HOME` maps to `$HOME/.config`.) |

### Verbosity Options

The following generic options affect the amount of information written to standard output and log files:

| Option      | Short Version | Description                                    |
|:------------|:--------------|:-----------------------------------------------|
| `--debug`   |               | **DEPRECATED** Enables trace-level debugging (alias for `--verbose`). Use the [log_level method][] instead. |
| `--silent`  |               | Don't write logs to standard out and standard error during startup. Recommended when starting `rippled` as a systemd unit to reduce redundant logging. |
| `--verbose` | `-v`          | **DEPRECATED** Enables trace-level debugging. Use the [log_level method][] instead. |



## Daemon Mode Options

```bash
rippled [OPTIONS]
```

Daemon mode is the default mode of operation for `rippled`. In addition to the [Generic Options](#generic-options), you can provide any of the following:

| Option              | Description                                            |
|:--------------------|:-------------------------------------------------------|
| `--fg`              | Run the daemon as a single process in the foreground. Otherwise, `rippled` forks a second process for the daemon while the first process runs as a monitor. |
| `--import`          | Before fully starting, import ledger data from another `rippled` server's ledger store. Requires a valid `[import_db]` stanza in the config file. |
| `--newnodeid`       | Generate a random node identity for the server. |
| `--nodeid {VALUE}`  | Specify a node identity. `{VALUE}` can also be a parameter associated with the container or hardware running the server, such as `$HOSTNAME`. |
| `--nodetoshard`     | Before fully starting, copy any complete [history shards](configuration/data-retention/history-sharding.md) from the ledger store into the shard store, up to the shard store's configured maximum disk space. Uses large amounts of CPU and I/O. Caution: this command copies data (instead of moving it), so you must have enough disk space to store the data in both the shard store and the ledger store. <!--{# Task for writing a tutorial to use this: DOC-1639 #}--> |
| `--quorum {QUORUM}` | This option is intended for starting [test networks](../concepts/networks-and-servers/parallel-networks.md). Override the minimum quorum for validation by requiring an agreement of `{QUORUM}` trusted validators. By default, the quorum for validation is automatically set to a safe number of trusted validators based on how many there are. If some validators are not online, this option can allow progress with a lower than normal quorum. **Warning:** If you set the quorum manually, it may be too low to prevent your server from diverging from the rest of the network. Only use this option if you have a deep understanding of consensus and have a need to use a non-standard configuration. |

The following option has been removed: `--validateShards`. {% badge href="https://github.com/XRPLF/rippled/releases/tag/1.7.0" %}Removed in: rippled 1.7.0{% /badge %}

## Stand-Alone Mode Options

```bash
rippled --standalone [OPTIONS]
rippled -a [OPTIONS]
```
Run in [stand-alone mode](../concepts/networks-and-servers/rippled-server-modes.md). In this mode, `rippled` does not connect to the network or perform consensus. (Otherwise, `rippled` runs in daemon mode.)

## Initial Ledger Options

The following options determine which ledger to load first when starting up. These options are intended for debugging and for starting networks. These options work with both stand-alone mode and network mode. By default, the server loads its initial ledger using a combination of saved local data and data downloaded from the peer-to-peer network based on what ledger has been most recently validated by the network.

| Option                | Description                                          |
|:----------------------|:-----------------------------------------------------|
| `--ledger {LEDGER}`   | Load the ledger version identified by `{LEDGER}` (either a ledger hash or a ledger index) as the initial ledger. The specified ledger version must be in the server's ledger store. |
| `--ledgerfile {FILE}` | Load the ledger version from the specified `{FILE}`, which must contain a complete ledger in JSON format. For an example of such a file, see the provided {% repo-link path="_api-examples/rippled-cli/ledger-file.json" %}`ledger-file.json`{% /repo-link %}. |
| `--load`              | Use only the ledger store on disk when loading the initial ledger. |
| `--net`               | Use only data from the network when loading the initial ledger. |
| `--replay`            | Use with `--ledger` to replay a specific ledger. Your server must have the ledger in question and its direct ancestor already in the ledger store. Using the previous ledger as a base, the server processes all the transactions in the specified ledger, resulting in a re-creation of the specified ledger. With a debugger, you can add breakpoints to analyze specific transaction processing logic. |
| `--start`             | Start with a new genesis ledger that has known amendments enabled, based on their default votes. This makes the functionality of those amendments available right away, instead of needing to wait two weeks for the [Amendment Process](../concepts/networks-and-servers/amendments.md). See also: [Start a New Genesis Ledger in Stand-Alone Mode](testing-and-auditing/start-a-new-genesis-ledger-in-stand-alone-mode.md). |
| `--valid`             | Consider the initial ledger a valid network ledger even before fully syncing with the network. This can be used for starting networks or rolling back an entire network to a known previous state, as long as 80% of that network's validators load the same ledger at around the same time. |

## Client Mode Options

```bash
rippled [OPTIONS] -- {COMMAND} {COMMAND_PARAMETERS}
```

In client mode, the `rippled` executable acts as a client to another `rippled` service. (The service may be the same executable running in a separate process locally, or it could be a `rippled` server on another server.)

To run in client mode, provide the [commandline syntax](../references/http-websocket-apis/api-conventions/request-formatting.md#commandline-format) for one of the [`rippled` API](../references/http-websocket-apis/index.md) methods.

Besides the individual commands, client mode accepts the [Generic Options](#generic-options) and the following options:

| Option                  | Description                                        |
|:------------------------|:---------------------------------------------------|
| `--rpc`                 | Explicitly specify that the server should run in client mode. Not required. |
| `--rpc_ip {IP_ADDRESS}` | Connect to the `rippled` server at the specified IP Address, optionally including a port number. |
| `--rpc_port {PORT}`     | **DEPRECATED** Connect to the `rippled` server on the specified port. Specify the port alongside the IP address using `--rpc_ip` instead. |

{% admonition type="success" name="Tip" %}Some arguments accept negative numbers as values. To ensure that arguments to API commands are not interpreted as options instead, pass the `--` argument before the command name.{% /admonition %}

Example usage (get account transaction history from the earliest available to latest available ledger versions):

```bash
rippled -- account_tx r9cZA1mLK5R5Am25ArfXFmqgNwjZgnfk59 -1 -1
```


## Unit Tests

```bash
rippled --unittest [OPTIONS]
rippled -u [OPTIONS]
```

Unit testing runs tests built into the `rippled` source code to confirm that the executable performs as expected. After running unit tests, the process displays a summary of results and exits. Unit tests cover functionality such as built-in data types and transaction processing routines.

If unit testing reports a failure, that generally indicates one of the following:

- A problem occurred when compiling `rippled` and it is not functioning as intended
- The source code for `rippled` contains a bug
- A unit test has a bug or has not been updated to account for new behavior

While running unit tests, you can specify the [Generic Options](#generic-options) and any of the following options:

| Option                             | Short Version | Description             |
|:-----------------------------------|:--------------|:------------------------|
| `--unittest-ipv6`                  |               | Use [IPv6](https://en.wikipedia.org/wiki/IPv6) to connect to the local server when running unit tests. If not provided, unit tests use IPv4 instead. |
| `--unittest-jobs {NUMBER_OF_JOBS}` |               | Use the specified number of processes to run unit tests. This can finish running tests faster on multi-core systems. The `{NUMBER_OF_JOBS}` should be a positive integer indicating the number of processes to use. |
| `--unittest-log`                   |               | Allow unit tests to write to logs even if `--quiet` is specified. (No effect otherwise.) |
| `--quiet`                          | `-q`          | Print fewer diagnostic messages when running unit tests. |


### Specific Unit Tests

```bash
rippled --unittest={TEST_OR_PACKAGE_NAME}
```

By default, `rippled` runs all unit tests except ones that are classified as "manual". You can run an individual test by specifying its name, or run a subset of tests by specifying a package name.

Tests are grouped into a hierarchy of packages separated by `.` characters and ending in the test case name.

#### Printing Unit Tests

```bash
rippled --unittest=print
```

The `print` unit test is a special case that prints a list of available tests with their packages.

#### Manual Unit Tests

Certain unit tests are classified as "manual" because they take a long time to complete. These tests are marked with `|M|` in the output of the `print` unit test. Manual tests do not run by default when you run all unit tests or a package of unit tests. You can run manual tests individually by specifying the name of the test. For example:

```bash
$ ./rippled --unittest=ripple.tx.OversizeMeta
ripple.tx.OversizeMeta
Longest suite times:
   60.9s ripple.tx.OversizeMeta
60.9s, 1 suite, 1 case, 9016 tests total, 0 failures
```

#### Providing Arguments to Unit Tests

Certain manual unit tests accept an argument. You can provide the argument with the following option:

| Option                  | Description                                        |
|:------------------------|:---------------------------------------------------|
| `--unittest-arg {ARG}`  | Provide the argument `{ARG}` to the unit test(s) currently being run. Each unit test that accepts arguments defines its own argument format.  |

{% raw-partial file="/docs/_snippets/common-links.md" /%}
