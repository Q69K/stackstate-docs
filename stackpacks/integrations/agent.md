---
title: Agent StackPack
kind: documentation
---

# StackState agent

## What is the Agent StackPack?

The StackState Agent provides the following functionality:

* Reporting hosts, processes and containers
* Reporting all network connections between processes/containers including network traffic telemetry
* Telemetry for hosts, processes, and containers
* 100+ additional integrations

The StackState Agent is open source: [view the source code on GitHub](https://github.com/StackVista/stackstate-agent).

## Supported configurations

The StackState Agent is supported on the following platforms:

| OS | Release | Arch | Network Tracer | Status | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Ubuntu | Trusty \(14\) | 64bit | - | OK | - |
| Ubuntu | Xenial \(16\) | 64bit | OK | OK | - |
| Ubuntu | Bionic \(18\) | 64bit | OK | OK | - |
| Debian | Wheezy \(7\) | 64bit | - | - | Needs glibc upgrade to 2.17 |
| Debian | Jessie \(8\) | 64bit | - | OK | - |
| Debian | Stretch \(9\) | 64bit | OK | OK | - |
| CentOS | 6 | 64bit | - | OK | Since version 2.0.2 |
| CentOS | 7 | 64bit | - | OK | - |
| RHEL | 7 | 64bit | - | OK | - |
| Fedora | 28 | 64bit | OK | OK | - |
| Windows | 2016 | 64bit | OK | OK | - |

## Installation

Install the StackState Agent with one of the following commands:

### Linux

Using [cURL](https://curl.haxx.se)

```text
curl -o- https://stackstate-agent-2.s3.amazonaws.com/install.sh | STS_API_KEY="{{config.apiKey}}" STS_URL="{{config.baseUrl}}/stsAgent" bash
```

Using [wget](https://www.gnu.org/software/wget/)

```text
wget -qO- https://stackstate-agent-2.s3.amazonaws.com/install.sh | STS_API_KEY="{{config.apiKey}}" STS_URL="{{config.baseUrl}}/stsAgent" bash
```

#### Linux offline installation

On your host, download the installation script from [https://stackstate-agent-2.s3.amazonaws.com/install.sh](https://stackstate-agent-2.s3.amazonaws.com/install.sh).

By default, installer tries to configure the package update channel, which would allow to update packages using the host package manager. If you for any reason do not want this behavior, please include `STS_INSTALL_NO_REPO=yes` as an environment parameter:

```text
STS_API_KEY="{{config.apiKey}}" STS_URL="{{config.baseUrl}}/stsAgent" STS_INSTALL_NO_REPO=yes install.sh PATH_TO_PREDOWNLOADED_INSTALLER_PACKAGE
```

### Windows

Using [PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-windows-powershell?view=powershell-6)

```text
. { iwr -useb https://stackstate-agent-2.s3.amazonaws.com/install.ps1 } | iex; install -stsApiKey "{{config.apiKey}}" -stsUrl "{{config.baseUrl}}/stsAgent"
```

#### Windows offline installation

On your host, download a copy of the PowerShell script from [https://stackstate-agent-2.s3.amazonaws.com/install.ps1](https://stackstate-agent-2.s3.amazonaws.com/install.ps1) alongside with the agent installer in the `.msi` form. The latest version of the installer can be downloaded from [https://stackstate-agent-2.s3.amazonaws.com/windows/stable/stackstate-agent-latest-1-x86\_64.msi](https://stackstate-agent-2.s3.amazonaws.com/windows/stable/stackstate-agent-latest-1-x86_64.msi).

Assuming, your installer is saved as `C:\stackstate-custom.msi`, and the PowerShell script saved as `C:\install_script.ps1`, open PowerShell with elevated priviledges and invoke the following set of commands:

```text
Import-Module C:\install_script.ps1
install -stsApiKey {{config.apiKey}} -stsUrl {{config.baseUrl}}/stsAgent -f C:\\stackstate-custom.msi
```

### Docker

#### Installation

To run the StackState Agent as a docker container, use the following configuration:

```text
stackstate-agent:
    image: docker.io/stackstate/stackstate-agent-2:latest
    network_mode: "host"
    pid: "host"
    privileged: true
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock:ro"
      - "/proc/:/host/proc/:ro"
      - "/sys/fs/cgroup/:/host/sys/fs/cgroup:ro"
      - "/etc/passwd:/etc/passwd:ro"
      - "/sys/kernel/debug:/sys/kernel/debug"
    environment:
      STS_API_KEY: "API_KEY"
      STS_STS_URL: "https://master.test.stackstate.io/receiver/stsAgent"
      STS_PROCESS_AGENT_URL: "https://master.test.stackstate.io/receiver/stsAgent"
      STS_PROCESS_AGENT_ENABLED: "true"
      STS_NETWORK_TRACING_ENABLED: "true"
      STS_APM_URL: "https://master.test.stackstate.io/receiver/stsAgent"
      STS_APM_ENABLED: "true"
      HOST_PROC: "/host/proc"
      HOST_SYS: "/host/sys"
```

#### Using Self-Signed Certificate

When checks are being configured to use a self-signed certificate for https requests, then the following environment variable should be overwritten:

```text
  CURL_CA_BUNDLE = ""
```

####  Advanced Configurations

Process reported by the StackState Agent can be filtered using a blacklist. Using it in conjunction with the inclusion rules will include otherwise excluded processes.

| Parameter | Mandatory | Default Value | Description |
| :--- | :--- | :--- | :--- |
| STS\_PROCESS\_BLACKLIST\_PATTERNS | No | [See  Github](https://github.com/StackVista/stackstate-process-agent/blob/master/config/config_nix.go) | A list of regex patterns that will exclude a process if matched |
| STS\_PROCESS\_BLACKLIST\_INCLUSIONS\_TOP\_CPU | No | 0 | Number of processes to report that have a high CPU usage |
| STS\_PROCESS\_BLACKLIST\_INCLUSIONS\_TOP\_IO\_READ | No | 0 | Number of processes to report that have a high IO read usage |
| STS\_PROCESS\_BLACKLIST\_INCLUSIONS\_TOP\_IO\_WRITE | No | 0 | Number of processes to report that have a high IO write usage |
| STS\_PROCESS\_BLACKLIST\_INCLUSIONS\_TOP\_MEM | No | 0 | Number of processes to report that have a high Memory usage |
| STS\_PROCESS\_BLACKLIST\_INCLUSIONS\_CPU\_THRESHOLD | No |  | Threshold that enables the reporting of high CPU usage processes |
| STS\_PROCESS\_BLACKLIST\_INCLUSIONS\_MEM\_THRESHOLD | No |  | Threshold that enables the reporting of high Memory usage processes |

Certain features of the agent can be turned off if not needed:

| Parameter | Mandatory | Default Value | Description |
| :--- | :--- | :--- | :--- |
| STS\_PROCESS\_AGENT\_ENABLED | No | True | Whenever process agent should be enabled |
| STS\_APM\_ENABLED | No | True | Whenever trace agent should be enabled |
| STS\_NETWORK\_TRACING\_ENABLED | No | True | Whenever network tracer should be enabled |

#### Troubleshooting

To troubleshoot the StackState Agent container, set the logging level to `debug` using the `STS_LOG_LEVEL` environment variable:

```text
STS_LOG_LEVEL: "DEBUG"
```

## Configuration

#### Linux

The configuration file for the StackState Agent is located at `/etc/stackstate-agent/stackstate.yaml`

Configuration files for integrations are located in `/etc/stackstate-agent/conf.d/`

#### Windows

The configuration file for the StackState Agent is located at `C:\ProgramData\StackState\stackstate.yaml`

Configuration files for integrations are located in `C:\ProgramData\StackState\conf.d`

## Troubleshooting

Try running the info command to see the state of the StackState Agent.

#### Linux

Logs for the subsystems are in the following files:

```text
/var/log/stackstate-agent/agent.log
/var/log/stackstate-agent/process-agent.log
```

#### Windows

Logs for the subsystems are in the following files:

```text
﻿C:\ProgramData\StackState\logs\agent.log
﻿C:\ProgramData\StackState\logs\process-agent.log
```

## Starting and Stopping the StackState Agent

#### Linux

To manually start the StackState Agent:

```text
sudo service stackstate-agent start
```

To stop the StackState Agent:

```text
sudo service stackstate-agent stop
```

To restart the StackState Agent and to reload the configuration files:

```text
sudo service stackstate-agent restart
```

#### Windows

Following commands require elevated privileges:

* Manually start the StackState Agent with CMD:

  ```text
  "C:\Program Files\StackState\StackState Agent\embedded\agent.exe" start-service
  ```

* Manually start the StackState Agent with PowerShell:

  ```text
  & "C:\Program Files\StackState\StackState Agent\embedded\agent.exe" start-service
  ```

* Manually stop the StackState Agent with CMD:

  ```text
  "C:\Program Files\StackState\StackState Agent\embedded\agent.exe" stopservice
  ```

* Manually stop the StackState Agent with PowerShell:

  ```text
  & "C:\Program Files\StackState\StackState Agent\embedded\agent.exe" stopservice
  ```

* Manually restart StackState Agent and to reload configuration files with CMD:

  ```text
  "C:\Program Files\StackState\StackState Agent\embedded\agent.exe" restart-service
  ```

* Manually restart StackState Agent and to reload configuration files with PowerShell:

  ```text
  & "C:\Program Files\StackState\StackState Agent\embedded\agent.exe" restart-service
  ```

Below commands need to be invoked from `"C:\Program Files\StackState\StackState Agent\embedded"` directory.

To manually start the StackState Agent:

```text
"./agent.exe start-service"
```

To stop the StackState Agent:

```text
"./agent.exe stopservice"
```

To restart the StackState Agent and to reload the configuration files:

```text
"./agent.exe restart-service"
```

## Status and Information

#### Linux

To check if the StackState Agent is running and receive information about the Agent's state:

```text
sudo service stackstate-agent status
```

Tracebacks for errors can be retrieved by setting the -v flag:

```text
sudo service stackstate-agent status -v
```

#### Windows

To check if the StackState Agent is running and receive information about the Agent's state:

```text
"./agent.exe status"
```

