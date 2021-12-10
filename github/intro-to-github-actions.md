# Intro to GitHub Actions

Overview from [the docs](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions)
> GitHub Actions is a continuous integration and continuous delivery (CI/CD) platform that allows you to automate your build, test, and deployment pipeline. You can create workflows that build and test every pull request to your repository, or deploy merged pull requests to production.

Components of GitHub Actions, also from [the docs](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions#the-components-of-github-actions)
>You can configure a GitHub Actions *workflow* to be triggered when an *event* occurs in your repository, such as a pull request being opened or an issue being created. Your workflow contains one or more *jobs* which can run in sequential order or in parallel. Each job will run inside its own virtual machine *runner*, or inside a container, and has one or more *steps* that either run a script that you define or run an *action*, which is a reusable extension that can simplify in your workflow.

I used a GitHub Action as part of the [ACG](https://acloudguru.com/) Hands-On GitOps course.

The code was forked from the [linuxacademy/content-gitops repo](https://github.com/linuxacademy/content-gitops)

[My copy](https://github.com/shanebsutton/content-gitops)

Workflows are defined in YAML files, which must be stored in the `.github/workflows` directory of the repo.  Actions also need to be enabled on the repository, and **when you fork a repo the actions are disabled by default**.

The use case for the lab was to build a Docker container image, which was also defined in the repo, and push it to the [Docker Hub](https://hub.docker.com/) container image library.

As defined, any merged pull request involving files in a specified directory triggered the action and pushed a new version of the container.

The rest of the lab involved using [Flux](https://fluxcd.io/) to syncronize a Kubernetes environment with code changes to different directories in the same repo.  This included pulling the latest image from the Docker Hub repo.

## References

- [Understanding GitHub Actions](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions)
- [GitHub Actions Docs](https://docs.github.com/en/actions)
