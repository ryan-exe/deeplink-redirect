# deeplink-redirect

A minimal GitHub Pages site that redirects to a mobile deep link via a URL parameter.

## Why

When testing an Expo app on device from a PR, Expo's GitHub Action posts a QR code, not ideal if you're already on your phone. The fix is to add a tappable link, but GitHub strips non-`https://` URLs from PR comments, breaking `exp://` and custom scheme links.

This page wraps your deep link in an `https://` URL that immediately redirects on tap, making it safe to post in GitHub comments and tappable on device.

## Usage
```
https://ryan-exe.github.io/deeplink-redirect/?link=<your-mobile-deep-link>
```

**Example:**
```
https://ryan-exe.github.io/deeplink-redirect/?link=exp://u.expo.dev/abc123/group/xyz789
```

## GitHub Actions

In your GHA workflow that comments on PRs, encode the Expo deep link as the `link` param:
```yaml
- name: Comment deep link on PR
  uses: actions/github-script@v7
  with:
    script: |
      const deepLink = `exp://u.expo.dev/...`;
      const redirectUrl = `https://ryan-exe.github.io/deeplink-redirect/?link=${encodeURIComponent(deepLink)}`;
      github.rest.issues.createComment({
        issue_number: context.issue.number,
        owner: context.repo.owner,
        repo: context.repo.repo,
        body: `[Open in Expo Go](${redirectUrl})`
      });
```

## How it works

`index.html` reads the `link` query param and calls `window.location.replace()` no frameworks, no dependencies, instant redirect.
