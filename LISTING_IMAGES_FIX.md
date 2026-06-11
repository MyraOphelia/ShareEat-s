# Why listing images don’t appear in Supabase (and how to fix)

Listing images are stored in **Supabase Storage** in the bucket **`listing-images`**. The app saves the **public URL** in `listings.image_url`. If the bucket isn’t public or policies are missing, the URL won’t load and images won’t appear.

---

## 1. Create the bucket and make it **Public**

1. In Supabase go to **Storage** (left sidebar).
2. If you don’t see a bucket named **`listing-images`**, click **New bucket**.
   - **Name:** `listing-images` (exactly).
   - **Public bucket:** turn **ON** (required so image URLs work in the browser).
3. Click **Create bucket**.

If the bucket already exists but images still don’t load:

- Open **Storage** → **listing-images** → click the **⋮** (three dots) → **Edit**.
- Ensure **Public bucket** is **ON**, then save.

---

## 2. Add Storage policies

Policies are required so:

- Sellers (authenticated users) can **upload** and **update/delete** their files.
- Anyone (including anonymous) can **read** images (so `<img src="...">` works).

**Option A – Run the fix script (recommended)**  
In **SQL Editor**, run the contents of **`supabase-listing-images-policies.sql`** (in this project). That will create or replace the correct policies.

**Option B – Use the Dashboard**  
1. Go to **Storage** → **listing-images** → **Policies**.
2. Add:

| Policy   | Allowed role   | Operation | Definition / check                          |
|----------|----------------|-----------|---------------------------------------------|
| Upload   | authenticated  | INSERT    | bucket_id = 'listing-images'                |
| Public read | public (anon) | SELECT    | bucket_id = 'listing-images'                |
| Update   | authenticated  | UPDATE    | bucket_id = 'listing-images'                |
| Delete   | authenticated  | DELETE    | bucket_id = 'listing-images'                |

---

## 3. Quick checklist

- [ ] Bucket name is exactly **`listing-images`**.
- [ ] Bucket is **Public** (Public bucket = ON).
- [ ] Policies exist for **INSERT** (authenticated) and **SELECT** (public).  
- [ ] You’ve run **`supabase-listing-images-policies.sql`** (or added the policies in the Dashboard).

After this, **create or edit a listing** and upload an image again. The image should appear in the app and in Supabase Storage under **listing-images** → your user id folder.

---

## 4. If it still doesn’t work

- **Browser console (F12):** Check for 403/404 on the image URL.  
  - 403 → policy or bucket visibility (must be public + SELECT for public).  
  - 404 → file not uploaded or wrong path; try uploading again from the listing form.
- **Table Editor → listings:** Confirm the row has an `image_url` like  
  `https://YOUR-PROJECT.supabase.co/storage/v1/object/public/listing-images/...`
- **Storage:** Open **listing-images** → your user UUID folder and confirm the image file is there.

If you paste an image URL manually in “Or paste image URL” when editing a listing, it must be a full URL (e.g. from Supabase Storage “Copy URL” or any public image link).
