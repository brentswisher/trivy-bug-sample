# trivy-bug-sample
This repository contains the example files needed to reproduce a bug in the trivy vulnerability scanning tool.

See https://github.com/aquasecurity/trivy/discussions/5565 for more information.

## Directions to recreate
### Failing Sample
1. Run `trivy fs failing/yarn.lock` 
2. See devcert is listed as having v1.1.0 installed when it should be 1.2.2
3. See vulnerabilities for devcert v1.1.1 listed even though v1.2.2 is actually being used
<img width="1160" alt="Screenshot 2023-11-13 at 3 05 18 PM" src="https://github.com/brentswisher/trivy-bug-sample/assets/6653970/3b2c0bed-e83e-4974-9806-6e1b27c4f927">

### Make sample pass by bumping wrong version
1. Run `trivy fs passing-higher-version/yarn.lock`
2. See devcert has found no vulnerabilities using the exact same version of devcert as the failing sample
<img width="1160" alt="Screenshot 2023-11-13 at 3 05 38 PM" src="https://github.com/brentswisher/trivy-bug-sample/assets/6653970/11d20ec7-4a3b-4de6-b33b-ce3a5afc22e0"> 

Because trivy now thinks devcert v4.0.0 (Which doesn't even exist) is installed, it finds no vulnerabilities.
If there were a devcert vulnerability discovered and patched in a major release, trivy would not report the vulnerability
because it would see it as fixed in v2.0.0 and thinks v4.0.0 is installed in this project.


### Make sample pass by not using a version number in the `dependenciesMeta` key.
1. Run `trivy fs passing-no-dependencies-meta-version/yarn.lock`
2. See devcert has found no vulnerabilities because it is correctly identifying devcert v1.2.2 as installed
<img width="1160" alt="Screenshot 2023-11-13 at 3 06 24 PM" src="https://github.com/brentswisher/trivy-bug-sample/assets/6653970/66dea2de-944a-4b61-b105-603b242c08df"> 


The default behavior of `yarn unplug <dependency>` is to use the specific version, but there appears to be a temporary
workaround By updating the `dependenciesMeta` key in your project's `package.json` to not include a specific version,
the `yarn.lock` file will be created without a specifc version number, and trivy can parse it correctly. 
