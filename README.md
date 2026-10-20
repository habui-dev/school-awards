# School Awards — online voting website

This is a static website intended for free hosting (for example, GitHub Pages) with Supabase Free for shared database and photo storage.

## Features
- The original 25 nominees and five categories.
- Public voting without sign-in; one submission inserts one vote.
- Shared results and shared nominee photos.
- Host signs in with a Supabase email/password account to upload photos and pause/reopen voting.
- Responsive layout for phones and computers.

## Important voting limitation
Because public voting requires no sign-in or identity check, this app cannot reliably enforce one vote per person. Visitors can submit multiple votes or use another browser/device. Results are a casual poll, not a secure election. Do not collect sensitive information.

## Setup overview
1. Create a Supabase project at https://supabase.com/ (free plan may have limits/pausing rules).
2. In Supabase, open SQL Editor and run the complete `supabase.sql` file.
3. In Supabase Authentication, create your host user (email + password). If email confirmation is enabled, confirm it.
4. Find your host user's UUID in Authentication > Users. In SQL Editor run:
   `insert into public.admins(user_id) values ('PASTE-HOST-USER-UUID-HERE');`
   Replace the placeholder with the actual UUID. This is what grants host privileges.
5. In Supabase Project Settings / API (or Connect), copy the Project URL and the anon/public key. Put them into `config.js`. Never use the `service_role` key in the website.
6. Test locally by opening the files through a local web server (recommended) or deploy to GitHub Pages.
7. For free public hosting: create a GitHub repository, upload `index.html`, `config.js`, and `supabase.sql` (the SQL file is not required by the website but keep it for setup notes). In repository Settings > Pages, select Deploy from a branch, choose `main` and `/ (root)`, save. Wait for the public URL.
8. In Supabase Authentication > URL Configuration, add your GitHub Pages URL to Site URL and Redirect URLs if needed. Password login usually works on the same page; configure the URL to match your published site.
9. Open the public website, sign in using the host email/password, and upload each photo from Host / Admin.
10. Test voting and live results in a private/incognito window before sharing.

## Files
- `index.html`: website interface and logic
- `config.js`: public Supabase URL and anon key; fill these in before publishing
- `supabase.sql`: database tables, policies, seed nominees, photo bucket, and results view

## Security notes
- The anon/public key is intended for frontend use. Security relies on database/storage Row Level Security (RLS) policies.
- Never publish your Supabase database password or service_role/secret key.
- The admin account UUID must be added to `public.admins`; creating an auth user alone does not grant admin rights.
- This setup intentionally allows public vote inserts and repeat voting. If you need one vote per student, use a school login, one-time code, or another verification method.
