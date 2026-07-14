<div align="center">

# ☁️ s3-automation

**Automate the repetitive AWS S3 + IAM setup — create a bucket, a scoped IAM user/group, a least-privilege policy, and access keys, in one `make` command.**

[![Amazon S3](https://img.shields.io/badge/Amazon%20S3-569A31?logo=amazons3&logoColor=white)](https://aws.amazon.com/s3/)
[![AWS IAM](https://img.shields.io/badge/AWS%20IAM-FF9900?logo=amazonaws&logoColor=white)](https://aws.amazon.com/iam/)
[![Bash](https://img.shields.io/badge/Bash-4EAA25?logo=gnubash&logoColor=white)](https://www.gnu.org/software/bash/)
[![AWS CLI](https://img.shields.io/badge/AWS%20CLI-232F3E?logo=amazonaws&logoColor=white)](https://aws.amazon.com/cli/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](#-contributing)

</div>

---

## Overview

Spinning up an S3 bucket with a properly scoped IAM user is fiddly and repetitive: create the bucket, create the user and group, write a folder-scoped policy, attach it, then generate access keys. `s3-automation` scripts that whole flow so you can stand up a **development** or **staging** bucket with correct, least-privilege access in seconds.

It ships two variants:

| Directory | Bucket type | Use for |
| :--- | :--- | :--- |
| [`s3_automation_private/`](s3_automation_private) | **Private** bucket | Internal/dev/staging data |
| [`s3_automation_public/`](s3_automation_public) | **Public**-read bucket | Static assets meant to be served publicly |

## ✨ Features

- 🪣 **Bucket creation** — creates the S3 bucket if it doesn't exist
- 👤 **IAM user + group** — provisions a dedicated user and group
- 🔒 **Least-privilege policy** — access scoped to a single folder in the bucket (see [`policy.json`](s3_automation_private/policy.json))
- 🔑 **Access keys** — generates an access key / secret and records them
- ♻️ **Idempotent** — re-running skips resources that already exist
- 🧩 **One command** — `make run_private` / `make run_public` orchestrates every step

## 🧰 Prerequisites

- An **AWS account** and credentials allowed to manage S3 + IAM
- [**AWS CLI**](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
- [**jq**](https://stedolan.github.io/jq/) (parses the generated keys)
- **make** and **bash**

```bash
# Debian/Ubuntu
sudo apt update && sudo apt install -y awscli jq make

# Amazon Linux / RHEL
sudo yum install -y awscli jq make

# Configure your AWS credentials
aws configure    # Access Key, Secret, region, output format
```

## 🚀 Installation

```bash
git clone https://github.com/jay-tank/s3_automation.git
cd s3_automation
```

## ⚙️ Configuration

1. Pick a variant directory (`s3_automation_private` or `s3_automation_public`).
2. Edit **`input.txt`** with your names:
   ```
   UserName:s3_private_user
   GroupName:s3_private_group
   FolderName:s3_private_folder
   ```
3. In **`policy.json`**, set the `$BUCKET_NAME` and `$FOLDER_NAME` placeholders to your bucket and folder.

## 🔧 Usage

```bash
# Private bucket
cd s3_automation_private
make run_private

# Public bucket
cd s3_automation_public
make run_public
```

Each run executes the steps in order and writes the created **UserName / AccessKey / SecretKey** to `output.txt`.

## 🧠 How it works

| Step | Script | Action |
| :--- | :--- | :--- |
| 1 | `1.bucket.sh` | Create the S3 bucket |
| 2 | `2.iam.sh` | Create the IAM user and group |
| 3 | `3.policy.sh` | Render `policy.json` and attach the folder-scoped policy |
| 4 | `4.key.sh` | Create an access key/secret and save to `output.txt` |

`all.sh` runs them in sequence; the `Makefile` is the entry point.

## 🔒 Security

> ⚠️ **`output.txt` will contain a live AWS Access Key and Secret Key** after step 4. It is **git-ignored** — never commit it, store the keys in a secret manager, and rotate/delete keys you no longer need.

- The attached IAM policy is **scoped to a single folder** in the bucket (least privilege) — review [`policy.json`](s3_automation_private/policy.json) before use.
- Run with an AWS profile that has only the permissions it needs.

## 🤝 Contributing

Contributions are welcome! Open an issue to discuss a change, or send a PR. Keep scripts POSIX-friendly and never commit generated credentials.

## 📄 License

Distributed under the MIT License — see [`LICENSE`](LICENSE).

---

<div align="center">
<sub>Built to make repetitive AWS S3/IAM setup boring and repeatable. ⭐ if it helped.</sub>
</div>
