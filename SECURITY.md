# Security Policy

## Reporting a Vulnerability

If you discover a security issue (including accidentally committed secrets), please open a private security advisory or contact the repository owner directly. Do not open a public issue that contains secret values.

## Credential Handling

- Never commit `.env` files, Kaggle tokens, API keys, or private keys.
- Use environment variables or a secrets manager.
- Rotate any credential that has ever appeared in a public commit or notebook cell.
- The research notebook previously contained a hardcoded Kaggle key that has been rotated. Always use your own Kaggle API token via environment variables or `~/.kaggle/kaggle.json`.

## Supported Versions

This is an active undergraduate research repository. Security fixes are applied to the latest commit on the default branch.
