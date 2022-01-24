# Using Tags in GitLab CI/CD

I created a test project in my company's GitLab to start playing with the [GitLab CI/CD quickstart](https://docs.gitlab.com/ee/ci/quick_start/). The doc mentions to ensure you have runners available, but it did not mention tags.

The first job would enter a `stuck` state with a message about no available runners. I did indeed have runners, but after a little digging realized that the jobs in `.gitlab-ci.yml` need to contain at least one of the tags associated with a runner. Makes sense...

In the `.gitlab-ci.yml` file I just needed to add the `tags` keyword:

```shell
build-job:
  stage: build
  tags:
    - dev_win
  script:
    - echo "Hello, $GITLAB_USER_LOGIN!"
```

This is documented [elsewhere](https://docs.gitlab.com/ee/ci/runners/configure_runners.html#use-tags-to-control-which-jobs-a-runner-can-run), just not in the Quickstart. And as it turns out, "when you register a runner its default behavior is to only pick tagged jobs".

## References

- [Get started with GitLab CI/CD](https://docs.gitlab.com/ee/ci/quick_start/)
- [Configuring Runners](https://docs.gitlab.com/ee/ci/runners/configure_runners.html#use-tags-to-control-which-jobs-a-runner-can-run)
