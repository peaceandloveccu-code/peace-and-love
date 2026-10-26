# Peace & Love Co-operative Credit Union — Website

A self-contained, mobile-responsive website for Peace & Love Co-operative Credit Union Ltd (Koforidua-Nankese, Eastern Region, Ghana), built as a single `index.html` file (no build step, no dependencies).

## What's inside

- Home, About (incl. Leadership & Team), Savings, Loans (with a repayment calculator), Membership application, Member Portal (coming soon), Financial Education, News & Announcements, member testimonials, FAQ, Contact, and legal pages
- Content sourced from the Society's Bye-Laws and Articles of Association (mission, vision, core values, membership eligibility, loan/savings rules, governance)
- Branding built from the organization's own logo
- A staff admin dashboard for editing site content, served separately from the public site (see below)

## Staff admin address

The public site has no admin link and won't open the admin page. Staff use the admin address instead:

- `admin.html` in this repository (for example `https://<github-pages-address>/admin.html`), or
- an `admin.` subdomain of the credit union's domain (for example `https://admin.yourdomain.com`), which serves the same page.

`admin.html` is an exact copy of `index.html` except that it asks search engines not to list it; the page detects that it is the admin address and shows only the staff sign-in and editing tabs. Regenerate it after changing `index.html`:

```sh
sed 's#<meta name="robots" content="index, follow">#<meta name="robots" content="noindex, nofollow">#' index.html > admin.html
```

## Content storage (Supabase)

Site content edited in the admin dashboard is stored in the Supabase project **plccu**:

| Table | Holds |
| --- | --- |
| `site_content` | One row (`id = 'site'`): contact settings, savings, loans, news, testimonials, FAQs and articles |
| `team_members` | One row per Leadership & Team member (rows with `deleted = true` hide a built-in member) |
| `admins` | Staff accounts allowed to edit the site |

Anyone can read; Row Level Security only lets signed-in users listed in `admins` add, change or remove content. The publishable key in `index.html` is designed to be public — never put a secret / `service_role` key in the page.

Anything not yet saved in Supabase falls back to the defaults built into `index.html`. If Supabase can't be reached (for example inside the Claude artifact preview), the page uses the Claude artifact's own storage instead.

### Adding a staff member who can edit the site

1. Supabase dashboard → **Authentication → Users → Add user → Create new user**. Enter their email and a password and tick **Auto Confirm User**.
2. Supabase dashboard → **SQL Editor**, run (with their email):

   ```sql
   insert into public.admins (user_id, email)
   select id, email from auth.users where email = 'staff@example.com';
   ```

3. They open the site's **Staff admin** link and sign in with that email and password.

To remove someone's editing rights: `delete from public.admins where email = 'staff@example.com';`

Recommended: **Authentication → Sign In / Providers → Email** — turn off "Allow new users to sign up", so accounts can only be created from the dashboard. (Sign-ups can't edit anything without an `admins` entry, but this keeps the user list tidy.)

## Running it

Open `index.html` directly in a browser, or serve the folder with any static file host (GitHub Pages, Netlify, etc.).

Placeholder content (rates, contact details, names) is marked in the page — look for dotted-underline text, or use the "Highlight replaceable content" toggle in the footer to find everything that still needs real information before launch.
