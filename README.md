# CloudFormation Template Version Update and Deployment via GitHub Actions

This repository contains a sample CloudFormation template and a GitHub Actions workflow that dynamically updates a version value before deploying the stack. It uses the following tools and steps:

1. **[aws-cfn-template-flip](https://github.com/awslabs/aws-cfn-template-flip)**:  
   Converts your CloudFormation template between YAML and JSON formats.
   
2. **[jq](https://stedolan.github.io/jq/)**:  
   Edits JSON files to update the specified field (in this case, the `ComponentVersions.Major.id` field).

## How It Works

1. **Version Update**:  
   The workflow takes a `new_version` input, which you can set manually or let it default. This value replaces the hard-coded version in the template’s `Mappings.ComponentVersions.Major.id` field.

2. **Template Conversion**:  
   The CloudFormation template (`template.yaml`) is converted into JSON (`template.json`) using `cfn-flip`. This step allows us to use `jq` for JSON manipulation.

3. **JSON Update**:  
   Using `jq`, we replace the `Mappings.ComponentVersions.Major.id` value in `template.json` with the provided `new_version`.

4. **Convert Back to YAML**:  
   The updated JSON is then converted back to the original YAML format (overwriting `template.yaml`), ensuring the template remains in its expected format for CloudFormation.

## Prerequisites

- **GitHub Actions Secrets**:  
  - `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` must be set in the repository's secrets so that the workflow can authenticate with AWS.
  
- **AWS IAM Permissions**:  
  The AWS user whose credentials are used in the workflow must have the appropriate IAM permissions for CloudFormation operations and for any services or resources being managed.

## Usage

1. **Triggering the Workflow**:  
   The workflow can be triggered by:
   - Pushing changes to the `main` branch.
   - Manually via the "Run workflow" button on GitHub Actions, where you can specify the `new_version`.

2. **Providing the Version**:
   If you run the workflow manually, you can provide `new_version` as an input. For example:
   ```bash
   # Example input: V1.2.4
   If not provided, the workflow uses a default value.

3. Reviewing Deployment Results:
   After the workflow completes, you can check the updated template.yaml in the repository’s Actions logs.

## Files
- `.github/workflows/deploy.yml`:
The GitHub Actions workflow that automates the version update and deployment.

- `template.yaml`:
The original CloudFormation template to be updated. This template will be updated with the new version value during the workflow run.

## Example
If you manually run the workflow with new_version=V1.3.0, the workflow:

1. Converts template.yaml to template.json.
2. Updates ComponentVersions.Major.id to V1.3.0.
3. Converts the template back to YAML (overwriting template.yaml).

## Further Customization
- You can adjust parameters in the deploy.yml workflow to fit your environment.
- If you use different versioning strategies (e.g., pulling version from tags or environment variables), you can modify the set_version step in the workflow.

## Troubleshooting
- Validation Errors:
If aws cloudformation validate-template fails, ensure the updated template is still valid YAML and that the Mappings structure is correct.

- Permissions:
Ensure your AWS credentials and IAM policies are correctly set up.
