## version: 2. Wenn du’s in dein Repo packst, läuft’s smooth. 
##Überwachung: Du sagst, welche Dateien/Typen (z.B. npm für JavaScript) es checken soll.
##Updates planen: Wie oft es schaut (z.B. wöchentlich) und ob es nur Major-Updates oder auch Patches macht.
##PR-Regeln: Automatische Labels, Reviews oder  Ignorieren bestimmter Versionen.
##Sicherheit: Priorisiert Fixes für bekannte Lücken.

name: CI
    on: [push]
    jobs:
      test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm test

name: CI/CD Pipeline
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
      - name: Install deps
        run: npm ci
      - name: Run tests
        run: npm test
      - name: Build
        run: npm run build

  deploy:
    needs: build-and-test  # Only if tests pass
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'  # Prod only on main
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to Vercel (or wherever)
        uses: amondnet/vercel-action@v25
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.ORG_ID }}
          vercel-project-id: ${{ secrets.PROJECT_ID }}