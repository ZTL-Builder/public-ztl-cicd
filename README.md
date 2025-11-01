# Public ZTL CI/CD Repository

This is the **public CI/CD repository** for building and distributing ZTL binaries from the private `rust-ztl` repository.

## 🏗️ Architecture

```
Private Repo (rust-ztl) → Triggers → Public Repo (public-ztl-cicd) → Builds → S3 Storage
```

## 🚀 How It Works

1. **Code changes** are made in the private `rust-ztl` repository
2. **Automatic triggers** or manual dispatches send build requests to this public repository
3. **This repository** checks out the private source code and compiles it
4. **Binaries** are built for multiple platforms and uploaded to S3

## 📋 Required Secrets

Configure these secrets in this repository's settings:

| Secret Name | Description | Example |
|-------------|-------------|---------|
| `PRIVATE_REPO_NAME` | Private repository name | `your-username/rust-ztl` |
| `PRIVATE_REPO_TOKEN` | GitHub token with repo access | `ghp_xxxxxxxxxxxx` |
| `AWS_ACCESS_KEY_ID` | AWS access key for S3 | `AKIAIOSFODNN7EXAMPLE` |
| `AWS_SECRET_ACCESS_KEY` | AWS secret key for S3 | `wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY` |

## 🔧 Configuration

### Environment Variables
- **APP_NAME**: `ztl` (binary name)
- **AWS_REGION**: `ap-south-1`
- **AWS_S3_BUCKET**: `ztl-exe`

### Supported Platforms
- **Linux**: x86_64, aarch64 (ARM64)
- **Windows**: x86_64
- **macOS**: x86_64 (Intel), aarch64 (Apple Silicon)

## 📦 Build Outputs

### S3 Structure
```
s3://ztl-exe/
├── customers/[CUSTOMER_NAME]/[TIMESTAMP]/
│   ├── linux/x86_64/
│   ├── linux/aarch64/
│   ├── windows/x86_64/
│   ├── macos/x86_64/
│   └── macos/aarch64/
└── releases/[TIMESTAMP]/
    └── [same structure]
```

### Artifacts
- **GitHub Actions**: Available for 90 days
- **S3 Storage**: Permanent storage with organized folder structure

## 🎯 Manual Builds

To trigger a manual build:

1. Go to **Actions** tab
2. Select **"Build from Private Repository"** workflow
3. Click **"Run workflow"**
4. Configure parameters:
   - **Private repo ref**: Branch/tag/commit to build
   - **Build platforms**: `linux,windows,macos` (comma-separated)
   - **Customer name**: Optional branding
   - **Upload to S3**: Enable/disable S3 uploads

## 🔍 Monitoring

- Check **Actions** tab for build status
- View **S3 bucket** for uploaded artifacts
- Monitor **build logs** for troubleshooting

## 🛡️ Security

- Source code remains in private repository
- Only compiled binaries are handled here
- Secure token-based authentication
- AWS credentials stored as encrypted secrets

---

**Note**: This repository only contains CI/CD workflows. The actual source code is in the private `rust-ztl` repository.
