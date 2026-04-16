# Contributing to the Ennote Agent Helm Chart

First off, thank you for considering contributing to Ennote! It's people like you that make open-source software secure, reliable, and powerful.

## Security Vulnerabilities
If you discover a security vulnerability, **do NOT open a public issue.** Please refer to our [SECURITY.md](SECURITY.md) for our responsible disclosure process.

## Development Process

1. **Fork the repository** and create your branch from `main`.
2. **Make your changes** in your feature branch.
3. **Test your changes.** If you add a new variable to `values.yaml`, you **must** regenerate the schema:
   ```bash
   # Install the schema plugin if you haven't already
   helm plugin install [https://github.com/dadav/helm-schema](https://github.com/dadav/helm-schema)
   
   # Run the generator
   helm schema
   ```
4. **Lint your chart.** We enforce strict linting to ensure quality. Run this before opening a PR:
   ```bash
   helm lint .
   ```
5. **Commit your changes.** Write clear, descriptive commit messages.
6. **Open a Pull Request.** Ensure your PR description clearly describes the problem and the solution. Link any relevant open issues.

## Pull Request Requirements

* All PRs must pass the automated GitHub Actions linting checks.
* If you are introducing a new feature, please update the `README.md` and `values.yaml` comments accordingly.
* We review PRs weekly and will provide constructive feedback!

## License
By contributing, you agree that your contributions will be licensed under its Apache 2.0 License.