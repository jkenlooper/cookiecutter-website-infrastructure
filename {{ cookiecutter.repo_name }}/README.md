# {{ cookiecutter.project_name }}

Creates infrastructure in AWS for the {{ cookiecutter.project_slug }} static website. Deploys
the following CloudFormation templates.

- `build-change-set.cfn.yaml`
- `security.cfn.yaml`
- `{{ cookiecutter.project_slug }}.cfn.yaml`
- `devops.cfn.yaml`

_It currently depends on another stack that creates many of the root resources._
That root stack is mainly used to setup the shared artifact and static website
buckets as well as many of the roles and other permissions.

## Initial Steps

Create a certificate in the us-east-1 region for {{ cookiecutter.site_domain }}.

Create the following in parameter store:

- /{{ cookiecutter.project_slug }}/example_public_key
- /{{ cookiecutter.project_slug }}/example_secret_key
- /shared/secret-header-string

Upload the templates to S3. And copy the URL for `build-change-set.cfn.yaml`.
This initial deploy-stack will fail to run the build since the CodeBuild project
doesn't exist yet.

```bash
./deploy-stack.sh
```

Manually create the {{ cookiecutter.project_slug }}-build-change-set stack using
the URL for `build-change-set.cfn.yaml`.

---

**[Change log](CHANGELOG.md)**

[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)
