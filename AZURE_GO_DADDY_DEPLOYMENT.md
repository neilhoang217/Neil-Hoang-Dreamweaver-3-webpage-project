# Simple Deployment Guide: Azure-Only Upload

**Goal:** Upload your soccer website files directly into Azure and host everything on Azure, without GitHub and without Azure CLI.

**What you're deploying:**
- `index.html` - Main comparison page
- `site-old/` - Legacy version
- `site-new/` - New Bootstrap version

## Important Choice

Your existing **Azure Static Web App** is not the best fit if you want to upload files only through the Azure Portal. Static Web Apps usually deploy from GitHub, Azure DevOps, or deployment tools.

For a true Azure-only web console upload, use **Azure Storage static website hosting**. It lets you upload files in the Azure Portal into a special container named `$web`.

---

# Step 1: Create A Storage Account

1. Go to `https://portal.azure.com/`.
2. Search for **Storage accounts**.
3. Click **Create**.
4. Use these settings:

```text
Resource group: choose your existing group, or create one
Storage account name: dreamweaversoccersite
Region: choose a nearby region
Performance: Standard
Redundancy: Locally-redundant storage (LRS)
```

5. Click **Review**.
6. Click **Create**.
7. Wait for Azure to finish.
8. Click **Go to resource**.

> Storage account names must be lowercase, no spaces, and globally unique. If `dreamweaversoccersite` is taken, add your initials or numbers.

---

# Step 2: Enable Static Website Hosting

1. Open your Storage Account in Azure Portal.
2. In the left menu, find **Data management**.
3. Click **Static website**.
4. Set **Static website** to **Enabled**.
5. Enter:

```text
Index document name: index.html
Error document path: index.html
```

6. Click **Save**.

Azure will create a container named:

```text
$web
```

This is where your website files go.

---

# Step 3: Upload Your Website Files

1. In the Storage Account left menu, click **Containers**.
2. Open the `$web` container.
3. Click **Upload**.
4. Upload `index.html`.
5. Click **Upload** again.
6. Upload the files inside `site-old/`.
7. Click **Advanced** before confirming the upload.
8. In **Upload to folder**, enter:

```text
site-old
```

9. Click **Upload**.
10. Repeat for `site-new/`.
11. In **Upload to folder**, enter:

```text
site-new
```

Your `$web` container should look like this:

```text
index.html
site-old/Soccer.htm
site-old/topfiveleagues.htm
site-old/futureleagues.htm
site-old/Image/...
site-new/Soccer.htm
site-new/topfiveleagues.htm
site-new/futureleagues.htm
site-new/style.css
site-new/Image/...
```

## Uploading Folders In The Portal

If Azure Portal lets you drag folders directly, drag these into `$web`:

```text
index.html
site-old
site-new
```

If the Portal only lets you select files, upload files in groups and use **Upload to folder** to preserve the folder names.

---

# Step 4: Open Your Website

1. In your Storage Account, click **Static website** again.
2. Copy the **Primary endpoint**.
3. Open that URL in your browser.

It will look similar to:

```text
https://dreamweaversoccersite.z13.web.core.windows.net/
```

Your pages will be:

```text
https://your-primary-endpoint/
https://your-primary-endpoint/site-old/Soccer.htm
https://your-primary-endpoint/site-new/Soccer.htm
```

The main endpoint should show your `index.html` comparison page.

---

# Step 5: Update The Website Later

When you edit files later:

1. Go to Azure Portal.
2. Open your Storage Account.
3. Go to **Containers**.
4. Open `$web`.
5. Click **Upload**.
6. Select the updated file.
7. Check **Overwrite if files already exist**.
8. Use the same folder path, such as:

```text
site-new
site-old
site-new/Image
site-old/Image
```

9. Click **Upload**.

---

# Optional: GoDaddy Domain

You can point a custom domain to the Azure Storage static website endpoint.

## Important HTTPS Note

Azure Storage static websites support HTTPS on the default Azure endpoint. For a custom domain like `www.yourdomain.com`, HTTPS usually requires Azure CDN or Azure Front Door.

If you only need the Azure URL, you can stop here.

## Basic GoDaddy CNAME Setup

1. In Azure Portal, open your Storage Account.
2. Go to **Static website**.
3. Copy the **Primary endpoint** hostname.

Example endpoint:

```text
https://dreamweaversoccersite.z13.web.core.windows.net/
```

Hostname only:

```text
dreamweaversoccersite.z13.web.core.windows.net
```

4. In GoDaddy, open your domain.
5. Go to **DNS** or **Manage DNS**.
6. Add a CNAME record:

```text
Type: CNAME
Name: www
Value: [dreamweaversoccersite.z13.web.core.windows.net](https://dreamweaversoccersite.z5.web.core.windows.net/)
TTL: Default
```

7. Save.
8. Wait 15-30 minutes.

Then test:

```text
http://www.yourdomain.com
```

For `https://www.yourdomain.com`, set up Azure CDN or Azure Front Door in front of the Storage static website.

---

# Troubleshooting

## The homepage does not load

Check that `index.html` is directly inside `$web`, not inside another folder.

Correct:

```text
$web/index.html
```

Incorrect:

```text
$web/Neil Hoang-Dreamweaver 3-webpage project/index.html
```

## Old or new version does not load

Check that these paths exist:

```text
$web/site-old/Soccer.htm
$web/site-new/Soccer.htm
```

## Images do not load

Check that these image folders exist:

```text
$web/site-old/Image/
$web/site-new/Image/
```

File names are case-sensitive in Azure Storage. Keep names exactly the same, including spaces:

```text
premier league.jpg
Liga_MX_logo.png
```

## Browser downloads HTML instead of showing it

Open the uploaded `.html` file in Azure Portal and check **Content-Type**.

Use:

```text
text/html
```

For CSS:

```text
text/css
```

For JavaScript:

```text
application/javascript
```

---

# Final Checklist

- [ ] Create Azure Storage Account
- [ ] Enable **Static website**
- [ ] Set index document to `index.html`
- [ ] Open `$web` container
- [ ] Upload `index.html`
- [ ] Upload `site-old/`
- [ ] Upload `site-new/`
- [ ] Open the **Primary endpoint**
- [ ] Optional: connect GoDaddy domain
