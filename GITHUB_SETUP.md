# Create a GitHub repository and push this project

Options below assume your local repository root is this project folder.

1) Quick (GitHub CLI `gh`) - recommended

```bash
# install GitHub CLI and authenticate: https://cli.github.com/
gh auth login

# create a new repo under your user (replace name and description as needed)
gh repo create RakshithGowdaM/agro_rent --public --source=. --remote=origin --push
```

2) Manual via Git (create repo on GitHub web then push)

```bash
git init
git add .
git commit -m "Initial commit"
# On GitHub, create a new repository named 'agro_rent' under your account.
git remote add origin https://github.com/RakshithGowdaM/agro_rent.git
git branch -M main
git push -u origin main
```

3) Create via GitHub API (use a Personal Access Token with `repo` scope)

```bash
# Replace $GH_TOKEN with your token; this creates the repo for the authenticated user
curl -H "Authorization: token $GH_TOKEN" https://api.github.com/user/repos -d '{"name":"agro_rent","private":false,"description":"AgriRent fullstack project"}'

# Then run the git commands above to add remote and push.
```

Notes:
- The `gh` approach is easiest and supports creating repo and pushing in one command.
- If you prefer the repo to be private, add `--private` for `gh` or set `"private": true` in the API payload.
- After pushing, enable GitHub Actions or add deployment integrations (Vercel / Render) per `DEPLOYMENT.md`.
