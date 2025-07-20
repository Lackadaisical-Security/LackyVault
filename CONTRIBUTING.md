# Contributing to LackyVault

Thank you for your interest in contributing to LackyVault! This document provides guidelines and instructions for contributing to the project.

## Code of Conduct

Please help us maintain a positive and inclusive environment by following our code of conduct:

- Be respectful and inclusive
- Exercise consideration and empathy
- Gracefully accept constructive criticism
- Focus on what is best for the community
- Show courtesy and respect towards other community members

## How to Contribute

### Reporting Bugs

1. Ensure the bug was not already reported by searching on GitHub under [Issues](https://github.com/LackadaisicalSecurity/LackyVault/issues)
2. If you're unable to find an open issue addressing the problem, open a new one with:
   - A clear title
   - A detailed description
   - As much relevant information as possible
   - A code sample or executable test case demonstrating the issue

### Suggesting Enhancements

1. Open a new issue:
   - Use a clear and descriptive title
   - Provide a detailed description of the enhancement
   - Explain why this enhancement would be useful
   - Include any relevant examples or mockups

### Code Contributions

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## Development Guidelines

### Zero Dependency Philosophy

LackyVault is built with a strict zero-dependency philosophy. This means:

- No external libraries or frameworks
- Only standard system APIs should be used
- All functionality should be implemented from scratch
- If you need a feature, build it; don't import it

### Security Requirements

- All code must follow security best practices
- Cryptographic implementations must be constant-time
- Memory handling must be secure (proper allocation, wiping)
- No leakage of sensitive information
- Follow the principle of least privilege

### Coding Style

- Use consistent indentation (4 spaces)
- Follow established naming conventions:
  - Functions: `snake_case`
  - Constants: `UPPER_SNAKE_CASE`
  - Structures: `snake_case_t` (with `_t` suffix)
  - Global variables: `g_prefixed_snake_case`
- Keep functions small and focused
- Add comments for complex sections
- Include appropriate license headers in all files

### Testing

- Add tests for new features
- Ensure all tests pass before submitting a PR
- Include test vectors for cryptographic implementations
- Write tests for both normal operations and edge cases

## Licensing

By contributing to LackyVault, you agree that your contributions will be licensed under the project's [BSD 3-Clause License](LICENSE).

## Need Help?

If you need help with the contribution process or have questions, please open an issue for assistance.

Thank you for contributing to LackyVault!

---

*"Because sometimes the best security is built from scratch"* 