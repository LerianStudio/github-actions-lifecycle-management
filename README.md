# github-actions-lifecycle-management

This GitHub Action publish helm releases in the Lerian Studio Plugin Lifecycle Management.

## Adding a New Chart

To enable this action for a new Helm chart, you need to update the `APPLICATION_IDS` repository variable.

1.  **Obtain the Application ID**:
    *   Navigate to your application within the Lerian Studio Lifecycle Management interface (distr).
    *   Find and copy the **Application ID** for your new chart.

2.  **Update `APPLICATION_IDS` Repository Variable**:
    *   Go to your GitHub repository's **Settings**.
    *   Navigate to **Secrets and variables** > **Actions**.
    *   Under **Repository variables**, find `APPLICATION_IDS`. If it doesn't exist, create it.
    *   Add or update the entry for your new chart in the format `chart_name:application_id`.
    *   If there are existing entries, separate them with a comma.

    **Example**:
    If you have an existing chart `midaz` with ID `1234`, and you're adding `new_chart` with ID `5678`, the `APPLICATION_IDS` variable should look like this:
    ```
    midaz:1234,new_chart:5678
    ```

## Inputs

The action uses the following inputs:

*   `chart_name` (required): The name of the Helm chart (e.g., `my-app`). This name must correspond to an entry in the `application_ids_from_workflow` input.
*   `version` (required): The version of the release (e.g., `1.2.3`).
*   `application_ids_from_workflow` (required): The string containing application ID mappings (e.g., `chart1:id1,chart2:id2`). This is typically passed from `vars.APPLICATION_IDS` in the calling workflow.
*   `lifecycle_api_token_from_workflow` (required): The API token for the Lerian Studio Lifecycle API. This is typically passed from `secrets.LIFECYCLE_API_TOKEN` in the calling workflow.

## Example Workflow

```yaml
name: Release Chart

on:
  push:
    tags:
      - 'v*' # Trigger on version tags like v1.0.0

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - name: Publish Release
        uses: LerianStudio/github-actions-lifecycle-management@main # Or your specific version
        with:
          chart_name: 'your-chart-name' # Replace with your chart name
          version: ${{ github.ref_name }} # Assumes tag is the version
          application_ids_from_workflow: ${{ vars.APPLICATION_IDS }}
          lifecycle_api_token_from_workflow: ${{ secrets.LIFECYCLE_API_TOKEN }}
```