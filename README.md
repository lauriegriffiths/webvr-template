# WebVR example project

This is an example project showing how to use Threlte and WebVR

## Development steps

- Install devbox
- curl -fsSL https://get.jetify.com/devbox | bash
- Use `devbox shell` to open a shell
- Use `pnpm install` to install packages
- Run the example project with `pnpm dev`

## Deployment setup

To deploy as a static site on github

- Make a new github repository
- set the `BASE_PATH` in `.env` to the name of your github repository.
- Add your respository as a remote
- Push to your repository

## Deploying elsewhere

- Build the site with `pnpm build`
- Copy the `build` directory to static hosting.
