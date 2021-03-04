# code-server-deploy-container

An image built for deploying code-server to [railway.app](https://railway.app), [Heroku](https://heroku.com), or other app engines.

![code-server running inside railway.app](../img/code-server-railway.png)

To launch your code-server environment, click the button below and log in with GitHub:

| [![Deploy on Railway](https://railway.app/button.svg)](https://railway.app/new?template=https%3A%2F%2Fgithub.com%2Fbpmct%2Fcode-server-railway&envs=PASSWORD%2CGIT_REPO&PASSWORDDesc=Your+password+to+log+in+to+code-server+with&GIT_REPODesc=A+git+repo+to+clone+and+open+in+code-server+%28ex.+https%3A%2F%2Fgithub.com%2Fcdr%2Fdocs.git%29) | [![Deploy on Heroku](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/bpmct/deploy-code-server/tree/main)                                |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| For Railway, it will ask you to make a new repo to store this image, so you can add additional software to your repo's `Dockerfile` in the future.                                                                                                                                                                                             | For Heroku, we recommend [using this repo as a template](https://github.com/bpmct/code-server-railway/generate) and using the one-click deploy on your own repo to further modify your app. |

---

## 💾 Persist your filesystem with `rclone`

This image has built-in support for [rclone](https://rclone.org/) so that your files don't get lost when code-server is re-deployed.

You can generate the rclone config on any machine, but it works great on the code-server environment itself, or Google Cloud Shell :)

```sh
# 1. install rclone
# see https://rclone.org/install/ for other install options
$ curl https://rclone.org/install.sh | sudo bash

# 2. create a new rclone remote with your favorite storage provider ☁️
$ rclone config

# 3. Encode your rclone config and copy to your clipboard
$ cat $(rclone config file | sed -n 2p) | base64 --wrap=0 # Linux
$ cat $(rclone config file | sed -n 2p) | base64 --b 0 # MacOS
```

Now, you can add the following the environment variables in the code-server cloud app:

| Environment Variable | Description                                                                                                                                           | Default Value                                | Required |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------- | -------- |
| RCLONE_DATA          | the encoded rclone config you copied in step 3                                                                                                        | n/a                                          | ✅       |
| RCLONE_REMOTE_NAME   | the name of the remote you added in step 2.<br />check with `$ rclone listremotes`                                                                    | code-server-remote                           |          |
| RCLONE_SOURCE        | source directory to sync files in the code-server container                                                                                           | the project directory: `/home/coder/project` |          |
| RCLONE_DESTINATION   | the path in the remote that rclone syncs to. change this if you have multiple code-server environments, or if you want to better organize your files. | code-server-files                            |          |

```sh

# How to use:

$ sh /home/coder/push_remote.sh # save your uncomitted files to the remote

$ sh /home/coder/pull_remote.sh # get latest files from the remote
```

---

## Todo:

- [ ] Make `push_remote` and `pull_remote` commands in path
- [ ] Impliment file watcher or auto file sync in VS Code
- [ ] Attach a "push" on a git stash??
- [ ] Add support for SSH / VS Code remote access
- [ ] Make rclone logs visible in environment for debugging