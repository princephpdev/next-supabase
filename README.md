This is a [Next.js](https://nextjs.org/) project bootstrapped with [`create-next-app`](https://github.com/vercel/next.js/tree/canary/packages/create-next-app).

This app has a profile page with magic link login functionality using [Supabase](https://supabase.com/)

# Node Version - 16

## Getting Started

Take a clone from [Github - next-supabase](https://github.com/princephpdev/next-supabase) to your local machine and open clone on vscode

- Run `npm install`

Open [https://app.supabase.io/](https://app.supabase.io/) with your browser and login

1. Create a new project
2. Go to the "Settings" section.
3. Click "API" in the sidebar.
4. Find your API URL in this page.
5. Find your "anon" and "service_role" keys on this page.

Go to your cloned project and then copy `env.example` file as `env.local`, add api keys taken from supabase.

## Add SQL to Supabase for user and profile

Go to **SQL**

    -- Create a table for public "profiles"
    create table profiles (
    id uuid references auth.users not null,
    updated_at timestamp with time zone,
    username text unique,
    avatar_url text,
    website text,

    primary key (id),
    unique(username),
    constraint username_length check (char_length(username) >= 3)
    );

    alter table profiles enable row level security;

    create policy "Public profiles are viewable by everyone."
    on profiles for select
    using ( true );

    create policy "Users can insert their own profile."
    on profiles for insert
    with check ( auth.uid() = id );

    create policy "Users can update own profile."
    on profiles for update
    using ( auth.uid() = id );

    -- Set up Realtime!
    begin;
    drop publication if exists supabase_realtime;
    create publication supabase_realtime;
    commit;
    alter publication supabase_realtime add table profiles;

    -- Set up Storage!
    insert into storage.buckets (id, name)
    values ('avatars', 'avatars');

    create policy "Avatar images are publicly accessible."
    on storage.objects for select
    using ( bucket_id = 'avatars' );

    create policy "Anyone can upload an avatar."
    on storage.objects for insert
    with check ( bucket_id = 'avatars' );

**_OR_**

1. Go to the "SQL" section.
2. Click "User Management Starter".
3. Click "Run".

Then, run the development server:

```bash
npm run dev
# or
yarn dev
```

Open [http://localhost:3000](http://localhost:3000) with your browser to see the result.

## Supabase

To learn more about Supabase, take a look at the following resources:

- [Supabase Documentation](https://supabase.com/docs) - learn about Supabase features and API.
