# CLI for JFrog Curation

## Overview

JFrog Curation defends your software supply chain, enabling early blocking of malicious or risky open-source packages before they even enter. Seamlessly identify harmful, vulnerable, or risky packages, ensuring increased security, compliance, and developer productivity.

For more information see [**here**](https://jfrog.com/curation/)

The 'curation-audit' is a JFrog CLI command designed for developers to scan their projects and identify third-party dependencies that violate the restrictions set by the Curation service. This command provides detailed insights into the specific package policies that are being violated, leading to their blockage by the Curation service. Additionally, when feasible, 'curation-audit' may suggest alternative versions of the packages that comply with the Curation policies.

Moreover, curation-audit supports waiver requests for eligible violations. If configured in the policy, developers can select the blocked package and request a waiver from the policy owner.

## Supported package managers & build systems

Curation-audit command supported package managers and build systems:

* Npm (npm)
* Maven (mvn) - Requires xray 3.92 and above, and Artifactory 7.82 and above
* Pip (pip) - Requires xray 3.92 and above, and Artifactory 7.82 and above
* Go (go) - Requires xray 3.92 and above, and Artifactory 7.87 and above

For a full list of the package managers and build systems supported by the curation-audit command and the required Artifactory and Xray versions to use it please see [**this matrix**](https://jfrog.com/help/r/jfrog-curation/curation-support-matrix)

***

### Commands

Audit your Project with JFrog CLI curation-audit command

### Setup:

Prerequisites:

Some package types (except npm packages) require 'pass-through' curation configuration on the remote repositories in Artifactory, in addition to configuring curation on them. For more information, see [**this page**](https://jfrog.com/help/r/jfrog-curation/configure-curation-pass-through)**.**

1.  **Connect JFrog CLI to JFrog Platform**

    Connect the JFrog CLI to your JFrog Platform instance by running the following command:

    ```
    jf c add
    ```

    * When prompted for the access token, use the token generated from Artifactory. For more details, refer to the [adding and editing configured servers documentation](https://docs.jfrog-applications.jfrog.io/jfrog-applications/jfrog-cli/configurations/jfrog-platform-configuration#adding-and-editing-configured-servers).

    ```
    jf c show
    ```

    * It should present Artifactory server just added (with default true)
2. **Configure JFrog CLI for Project**\
   Ensure your project is configured in the JFrog CLI with the repository you would like to resolve dependencies from. Here are details for each package manager:
   * **NPM:**
     * Set the resolved repository using the [**jf npmc**](https://docs.jfrog-applications.jfrog.io/jfrog-applications/jfrog-cli/binaries-management-with-jfrog-artifactory/package-managers-integration#setting-npm-repositories) command inside the project directory.
   * **MAVEN:**
     * Set the resolved repository using the [**jf mvnc**](https://docs.jfrog-applications.jfrog.io/jfrog-applications/jfrog-cli/binaries-management-with-jfrog-artifactory/package-managers-integration#setting-maven-repositories) command inside the project directory.
   * **PIP:**
     * Set the resolved repository using the [**jf pipc**](https://docs.jfrog-applications.jfrog.io/jfrog-applications/jfrog-cli/binaries-management-with-jfrog-artifactory/package-managers-integration#setting-python-repository) command inside the project directory (The only package installer supported for now by Python is "pip").
   * **GO:**
     * Set the resolved repository using the [**jf goc**](https://docs.jfrog-applications.jfrog.io/jfrog-applications/jfrog-cli/binaries-management-with-jfrog-artifactory/package-managers-integration#examples-4) command inside the project directory.

#### Commands Params

|                       |                                                                                                                                       |
|-----------------------|---------------------------------------------------------------------------------------------------------------------------------------|
| **Command name**      | curation-audit                                                                                                                        |
| **Abbreviation**      | ca                                                                                                                                    |
| **Command options**   |                                                                                                                                       |
| `--format`            | <p>[Default: table]<br><br>Defines the output format of the command. Acceptable values are: table and json.</p>                       |
| `--working-dirs`      | <p>[Optional]<br><br>A comma separated list of relative working directories, to determine the audit targets locations.</p>            |
| `--threads`           | <p>[Default: 3]<br><br>The number of parallel threads used to determine the curation status for each package in the project tree.</p> |
| `--requirements-file` | <p>[Optional] [Pip]<br><br>Defines pip requirements file name. For example: 'requirements.txt'</p>                                    |

#### Example 1

Curation-Audit the project in the current directory. Displays all known packages that were blocked by Curation Policies.

```
jf curation-audit
```

#### Example 2

Curation-Audit the projects according to the specific paths defined in the "working-dirs" option. Displays all known packages that were blocked by Curation Policies for all projects. The data is displayed in separate tables.

```
jf curation-audit --working-dirs="/path/to/project/npm_project1,/path/to/project/npm_project2"
```

#### Example 3

Curation-Audit the project in the current directory using 5 threads to check the packages Curation status in parallel. Displays all known packages blocked by Curation Policies.

```
jf curation-audit --threads=5
```

#### Example 4

Curation-Audit Waiver Request Process: The developer specifies the required row(s) from the table for the blocked policies. They then add a description and submit the request. A summary table is presented at the end of the process.

```
> jf curation-audit
Found 4 blocked packages for project sample-node-project:1.0.0
Curation
┌────┬──────────────┬────────────┬──────────────┬─────────┬─────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│ ID │ DIRECT       │ DIRECT     │ BLOCKED      │ BLOCKED │ PACKAGE │ VIOLATED     │ VIOLATED CON │ EXPLANATION  │ RECOMMENDATI │
│    │ DEPENDENCY   │ DEPENDENCY │ PACKAGE      │ PACKAGE │ TYPE    │ POLICY       │ DITION       │              │ ON           │
│    │ PACKAGE      │ PACKAGE    │ NAME         │ VERSION │         │ NAME         │ NAME         │              │              │
│    │ NAME         │ VERSION    │              │         │         │              │              │              │              │
├────┼──────────────┼────────────┼──────────────┼─────────┼─────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│ 1  │ ansi-regex   │ 3.0.0      │ ansi-regex   │ 3.0.0   │ npm     │ High CVE     │ CVE with CVS │ Package vers │ Upgrade to t │
│    │              │            │              │         │         │              │ S score betw │ ion contains │ he following │
│    │              │            │              │         │         │              │ een 7.0 and  │ the followin │ version(s):  │
│    │              │            │              │         │         │              │ 8.9 (with or │ g vulnerabil │ CVE-2021-380 │
│    │              │            │              │         │         │              │ without a fi │ ity(s):      │ 7: 6.0.1; 5. │
│    │              │            │              │         │         │              │ x version av │ CVE-2021-380 │ 0.1; 4.1.1;  │
│    │              │            │              │         │         │              │ ailable)     │ 7: 7.5       │ 3.0.1        │
└────┴──────────────┴────────────┴──────────────┴─────────┴─────────┴──────────────┴──────────────┴──────────────┴──────────────┘

Do you want to request a waiver for any of the listed packages? (y/n) [n]? y
Please enter the row number(s) for which you want to request a waiver (comma-separated for multiple, range, or “all”) [all]: all
Please enter the reason for the waiver request: Example request
```
