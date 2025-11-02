# Setup Guide for Two-Repository CI/CD

## 📋 Overview

This guide will help you set up the CI/CD pipeline between:
- **Private Repository**: `rust-ztl` (contains source code)
- **Public Repository**: `public-ztl-cicd` (handles builds and deployment)

## 🔧 Step 1: Configure Private Repository (`rust-ztl`)
ß
### 1.1 Add the Trigger Workflow
The trigger workflow should already be in your private repository at:
```
rust-ztl/.github/workflows/trigger-public-build.yml
```

### 1.2 Configure Secrets in Private Repository
Go to your private repository settings → Secsssßrets and variables → Actions

Add these **Repository Secrets**:

| Secret Name | Value | Description |
|-------------|-------|-------------|
| `PUBLIC_REPO_DISPATCH_TOKEN` | `ghp_xxxxxxxxxxxx` | GitHub Personal Access Token |
| `PUBLIC_REPO_NAME` | `your-username/public-ztl-cicd` | Your public repository name |

### 1.3 Create GitHub Personal Access Token
1. Go to GitHub Settings → Developer settings → Personal access tokens → Tokens (classic)
2. Click "Generate new token (classic)"
3. Select scopes: `repo` (Full control of private repositories)
4. Copy the token andß use it as `PUBLIC_REPO_DISPATCH_TOKEN`

## 🔧 Step 2: Configure Public Repository (`public-ztl-cicd`)

### 2.1 Repository Structure
Your public repository should have:
```
public-ztl-cicd/
├── .github/workflows/
│   └── build-from-private.yml
├── README.md
└── SETUP-GUIDE.md
```

### 2.2 Configure Secrets in Public Repository
Go to your public repository settings → Secrets and variables → Actions

Add these **Repository Secrets**:

| Secret Name | Value | Description |
|-------------|-------|-------------|
| `PRIVATE_REPO_NAME` | `your-username/rust-ztl` | Your private repository name |
| `PRIVATE_REPO_TOKEN` | `ghp_xxxxxxxxxxxx` | GitHub Personal Access Token |
| `AWS_ACCESS_KEY_ID` | `AKIAIOSFODNN7EXAMPLE` | AWS Access Key |
| `AWS_SECRET_ACCESS_KEY` | `wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY` | AWS Secret Key |

### 2.3 GitHub Token for Private Repository Access
You can use the same token as in Step 1.3, or create a new one with `repo` scope.

### 2.4 AWS Credentials Setup
1. Go to AWS IAM Console
2. Create a new user or use existing user
3. Attach policy for S3 access:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:PutObjectAcl",
                "s3:GetObject",
                "s3:DeleteObject"
            ],
            "Resource": "arn:aws:s3:::ztl-exe/*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket"
            ],
            "Resource": "arn:aws:s3:::ztl-exe"
        }
    ]
}
```
4. Generate access keys and add them as secrets

## 🧪 Step 3: Test the Setup

### 3.1 Manual Test from Private Repository
1. Go to your private repository (`rust-ztl`)
2. Navigate to **Actions** tab
3. Select **"Trigger Public Repo Build"** workflow
4. Click **"Run workflow"**
5. Use default parameters for initial test
6. Click **"Run workflow"** button

### 3.2 Verify Build in Public Repository
1. Go to your public repository (`public-ztl-cicd`)
2. Navigate to **Actions** tab
3. You should see a **"Build from Private Repository"** workflow running
4. Monitor the build progress

### 3.3 Check S3 Uploads
1. Go to AWS S3 Console
2. Navigate to `ztl-exe` bucket
3. Look for new folders under `releases/[TIMESTAMP]/`
4. Verify binaries are uploaded

## 🔄 Step 4: Automatic Triggers

Once setup is complete, builds will automatically trigger when you:
- Push to `main`, `master`, or `develop` branches
- Push to `release/*` branches  
- Create tags matching `v*.*.*`

## 🐛 Troubleshooting

### Common Issues

**1. "Repository not found" error**
- Check `PRIVATE_REPO_NAME` and `PUBLIC_REPO_NAME` format: `username/repo-name`
- Verify tokens have correct permissions

**2. "Authentication failed" error**
- Regenerate GitHub tokens
- Ensure tokens have `repo` scope
- Check token hasn't expired

**3. "S3 upload failed" error**
- Verify AWS credentials are correct
- Check S3 bucket exists and is accessible
- Ensure IAM permissions are properly configured

**4. Build doesn't trigger**
- Check workflow file syntax
- Verify repository dispatch token permissions
- Look at private repository's Actions tab for errors

### Debug Steps

1. **Check workflow logs** in both repositories
2. **Verify all secrets** are configured correctly
3. **Test tokens manually** using GitHub API
4. **Check S3 permissions** using AWS CLI

## 📚 Additional Resources

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Repository Dispatch Events](https://docs.github.com/en/rest/repos/repos#create-a-repository-dispatch-event)
- [AWS S3 IAM Policies](https://docs.aws.amazon.com/s3/latest/userguide/example-policies-s3.html)

## 🎯 Next Steps

After successful setup:
1. **Customize build parameters** as needed
2. **Add notification webhooks** for build status
3. **Set up monitoring** for failed builds
4. **Configure branch protection** rules
5. **Add integration tests** to the pipeline

---

**Need Help?** Check the workflow logs in both repositories' Actions tabs for detailed error messages.
