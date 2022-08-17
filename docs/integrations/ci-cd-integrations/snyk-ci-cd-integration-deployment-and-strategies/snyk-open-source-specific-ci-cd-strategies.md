# Snyk Open Source-specific CI/CD strategies

## Snyk Open Source-specific strategies

These strategies are useful to teams using the Snyk SCA ([Software Composition Analysis](https://snyk.io/blog/what-is-software-composition-analysis-sca-and-does-my-company-need-it/)) testing features.

### Gradle and Scala

* For "multi-project" configurations, test all sub-projects. Use this option with the `monitor` or `test` command: `--all-sub-projects`.
* To scan specific configurations, select certain values of configuration attributes to resolve the dependencies. Use this option with the `test` or `monitor` command: `--configuration-attributes=`.

### Python

*   Snyk uses Python to scan and find your dependencies. Snyk needs the Python version to start scanning, and defaults to "python." If you are using multiple Python versions, use this option with the `test` or `monitor` command to specify the correct Python command for execution: `--command=`. Example:

    ```
    snyk test --command=python3
    ```
* If you scan a Pip project and use the `--file=` option because your manifest file is not the standard `requirements.txt`, you must use the following option to specify Pip as the package manager `--package-manager=pip.`

### .Net

If you use a `.sln` file, you can specify the path to the file, and Snyk scans all the sub projects that are part of the repo, for example:

```
snyk test --file=sln/.sln
```

### Yarn

For Yarn workspaces use the `--yarn-workspaces` option to test and monitor your packages. The root lockfile will be referenced for scans of all the packages. Use the `--detection-depth` option to find sub-folders that are not auto discovered by default.

{% hint style="info" %}
**Note**\
Yarn workspaces support is for the `snyk test` and `snyk monitor` commands only at this time.
{% endhint %}

Example: scan only the packages that belong to any discovered workspaces in the current directory and 5 sub-directories deep.

```
snyk test --yarn-workspaces --detection-depth=6
```

You can use a common [`.snyk` policy file](../../../snyk-cli/test-for-vulnerabilities/the-.snyk-file.md) if you maintain ignores and patches in one place to be applied for all detected workspaces, by providing the policy path as follows:

```
snyk test --yarn-workspaces --policy-path=src/.snyk
```

### Monorepo

Some customers have complex projects, with multiple languages, package managers, and projects in a single repository. To facilitate this, you can take different approaches:

*   As you build **each project and language**, add a directive to run `snyk test` and target a specific project file, for example:

    ```
    snyk test --file=package.json
    ```

    After you install the dependencies of each project, make a similar call pointing to the specific artifact (such as `pom.xml`). This is fast and efficient, but can be difficult to scale, especially if you are not familiar with the project.
* When you use the `--all-projects` and `--detection-depth` options, the Snyk CLI or CI/CD plugin searches up to the `--detection-depth` in the folder structure for any manifests that match the supported file types. **Each project** is scanned and has its own result. Similarly, if you are using `snyk monitor,` a separate result is created for each project. This provides a good way to automate scanning, especially if you have projects spanning node, .net, python, and so on.
* For most **Gradle projects**, using `--all-projects` works, as it invokes gradle-specific options behind the scenes in the form of: `snyk test --file=build.gradle --all-sub-projects` when it finds the build file as part of the `--all-projects` search.
* **Gradle** may require additional configuration parameters. If so, target the other artifacts using `--file=` for each manifest of the other languages and package managers. You must then use `--all-sub-projects` and potentially `--configuration-matching` to scan a complex Gradle project.

See [Snyk for Java and Kotlin](../../../products/snyk-open-source/language-and-package-manager-support/snyk-for-java-gradle-maven.md) for more information.

##