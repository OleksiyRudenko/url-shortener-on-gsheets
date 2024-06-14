## URL Shortener with a Google Spreadsheet 📑

This is a simple web server that can redirect a pre-defined set of URLs in
Google Sheets. All you need to do is define a [Google Sheets][ex] like the
following, and deploy a simple app just by clicking around.

| shortcut | url |
|----|---|
| `code` | `https://github.com/ahmetb/` |
| `book` | `https://docs.google.com/forms/d/e/1FAIpQLSefArw8NWiha6YCaoTccGZmo4QvuDYY4s87Y_tjW6h4al_4NQ/viewform` |
| `yt` | `https://www.youtube.com/watch?v=dQw4w9WgXcQ`

**Costs:** This can be deployed to [Google Cloud Run](https://cloud.run) and run
for **free** thanks to [generous free tier][free tier] (+Google Sheets is free
with a Gmail account as well).

[free tier]: https://cloud.google.com/run/pricing

This redirector also supports path additions on top of base URLs, for example,
with the sheet row:

| shortcut | url |
|----|---|
| `gcp` | `https://github.com/GoogleCloudPlatform` |

The `go.ahmet.dev/gcp/golang-samples` will be redirected to
`https://github.com/GoogleCloudPlatform/golang-samples`.

## Setup

1. Create a new **Google Sheet**: https://sheets.new.

1. Add two columns, first column is the "shortcut", the second
   column is the "url" to redirect the user, third is "is clicked", forth is "clicked at". ([see example][ex])

1. Save the ID of your Sheet from the URL (it’s a random string
   that looks like `1SMeoyesCaGHRlYdGj9VyqD-qhXtab1jrcgHZ0irvNDs`).

1. Click to deploy to Cloud Run, and provide your **spreadsheet
   ID**, **sheet name** to monitor, **cache update frequency** (TTL), and **redirect destination** when root (`/`)
   is visited while deploying:

   [![Run on Google Cloud](https://deploy.cloud.run/button.svg)](https://deploy.cloud.run)

1. Go to https://console.cloud.google.com/run, click on
   `sheets-url-shortener` service. Find the email address written
   under `YAML` section (`serviceAccountName` property, emails looks similar to `123123123123-compute@developer.gserviceaccount.com`).

1. Go to your Google Sheets, click "Share" and give this email
   address "Editor" access on your sheet.

1. Go to https://console.developers.google.com/apis/api/sheets.googleapis.com/overview
   and make sure the Google Sheets API is enabled.

1. (Optional) If you want to use a custom domain like `go.ahmet.dev`, go to
   https://console.cloud.google.com/run/domains and map the
   `sheets-url-shortener` to your custom domain!

## Advanced Configuration

This server can be configured with these following parameters:

| Environment Variable | Description |
|-----|------|
| `SHEET_NAME` | Specify the sheet name. Default = `short-urls`. |
| `CACHE_TTL` | How frequently the spreadsheet must be reloaded. Default = `5s`. |
| `HOME_REDIRECT` | Which url to redirect to when root url (`/`) is visited. Default = `https://github.com/OleksiyRudenko/url-shortener-on-gsheets/` |
| `LISTEN_ADDR` | (optional) which network address to listen on (default `""` which means all interfaces). |
| `PORT` | (optional) http port to listen on (default `8080`). |
| `REDIRECT_STATUS` | (optional) HTTP status code to return upon redirect. (default `301`, Moved Permanently). |

## Disclaimer

This is not an official Google project. It's distributed as-is under Apache 2.0
License. See [LICENSE](./LICENSE).

[ex]: https://docs.google.com/spreadsheets/d/1wCcj0Y4wFUHGAY0DmyefFlQ3s1DR8aUTz9seykM2iJ8/edit?usp=sharing

## References

- [Google Cloud Deploy `app.json`](https://github.com/GoogleCloudPlatform/cloud-run-button#customizing-deployment-parameters)

### `Error: failed to push image to gcr.io/...: docker push failed: exit status 1, output:`

This solution is incorporated into `app.json` to overcome the error.

> If you get error
> `Error: failed to push image to gcr.io/url-shortener-for-nx2ch/url-shortener-on-gsheets: docker push failed: exit status 1, output:`
> ...
> `denied: Unauthenticated request. Unauthenticated requests do not have permission "artifactregistry.repositories.uploadArtifacts" on resource "projects/PROJECT_ID/locations/us/repositories/gcr.io" (or it may not exist)`
> then the solution would be to run the following command
> `gcloud auth configure-docker`
> and then re-run
> `docker push gcr.io/url-shortener-for-nx2ch/url-shortener-on-gsheets`
> (the command that failed producing the error messages above).
> 
> Solution source: https://github.com/GoogleCloudPlatform/cloud-run-button/issues/247


