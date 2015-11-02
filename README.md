# NateDown

## A Python3 fork of [mdwebhook](https://github.com/dropbox/mdwebhook)

This simple  app uses webhooks to get notified of new Markdown files in Dropbox. It then converts all Markdown files it sees to HTML, and copies the public link to the HTML file into an HTML comment at the top of the file. As of 20151101, this fork is minimally changed from the original work done by the Dropbox team, aside from being updated for Python3 and adding the link to the public file.

Read more about webhooks and this example on [the Dropbox developers site](https://www.dropbox.com/developers/webhooks/tutorial).

You can try the example yourself by visiting [natedown.herokuapp.com](https://natedown.herokuapp.com), or the Python2 version by checking out [the original mdwebhook](https://github.com/dropbox/mdwebhook).

## Running the sample yourself

This sample was built with Heroku in mind as a target, so the simplest way to run the sample is:

1. Install the [Heroku Toolbelt](https://toolbelt.heroku.com/)
2. Copy `.env_sample` to `.env` and fill in the values.
3. Run `pip3 install -r requirements.txt` to install the necessary modules.
4. Launch the app via `heroku local` or deploy to Heroku.

You can also just set the required environment variables (using `.env_sample` as a guide) and run the app directly with `python3 app.py`.

This Python3 version of `mdwebook` is also configured to read in a `dev_config.py` from an [instance folder](http://flask.pocoo.org/docs/0.10/config/#instance-folders) to facilitate local development and debugging. You can either copy your environment variables there or have it read in your `.env` file. For example:

```python
import os

DEBUG = True
try:
    with open(".env") as conf:
        for line in conf.read().splitlines():
            var, val = line.split('=')
            if not os.getenv(var):
                os.environ[var] = val
except FileNotFoundError:
    print("dev_config.py can't find `.env`. Are you sure you're running from "
          "the root directory?")
```

## Local debugging with [dropbox_hook](https://github.com/dropbox/dropbox_hook)

The Dropbox team has also made a script that helps simulate Dropbox webhook requests to help with local development and debugging. I've added it as a [git submodule](https://git-scm.com/docs/git-submodule). Setup and use example, assuming your development server is running at `http://127.0.0.1:5000`:

```bash
git submodule update --init
pip3 install -r dropbox_hook/requirements.txt
python3 dropbox_hook/dropbox_hook.py notify http://127.0.0.1:5000/webhook -s $APP_SECRET -u $USER_ID
```

The `APP_SECRET` is one of the environment variables you should already know, but the `USER_ID` is a bit trickier to figure out. To find it, I ended up modifying the `oauth_callback()` function to print `uid` to the screen in debug mode, then copied it down. If you can think of a better way (or preferably a one-liner to be run from shell that I could include here in the README), let me know, otherwise you can give that a shot.

## Deploy on Heroku

You can deploy directly to Heroku with the button below. First you'll need to create a Dropbox API app via the [App Console](https://www.dropbox.com/developers/apps). Make sure your app has access to files (not just datastores), and answer "Yes - My app only needs access to files it creates" to ensure your app gets created with "App folder" permissions.

[![Deploy](https://www.herokucdn.com/deploy/button.png)](https://heroku.com/deploy)

Once you've deployed, you can easily make your own customizations by forking this repo, `git clone` your fork to make a local copy, then `heroku create $MY_UNIQUE_APP_NAME` from that directory.

