---
layout: post
title: A weird Jupyter Notebook error...
---

I do hope to utilize this space for technical notes. Though my colleagues have long been praising how throughout and well-organized my documentations are at workspace, I found myself little motivation in documenting my life :P. Plus, I've learned a ton through this short summer internship, so it's necessary to jot down my thought processes along the way for future reference.

Now back to the topic. As a *data scientist* **intern** (LOL), Jupyter notebook is my good friend. Due to the large volumne of images that I need to process for the project, a remote GPU server is required. Thanks to my coworkers who put up a nice guide on how to work with Jupyter on a VPS (specifically tailored to our special case, as IT has put complicated rules to ensure data privacy - that's another post that I've reserved for the future), I was able to run Jupyter locally using the remote computing power. Everything worked fine until this morning, when I executed the command to mirror the notebook to a local port - the following unexpected error occured (similar, but not exactly the same):

```
Traceback (most recent call last):
  File "/opt/jupyter/bin/jupyter-notebook", line 11, in <module>
    sys.exit(main())
  File "/opt/jupyter/local/lib/python2.7/site-packages/jupyter_core/application.py", line 267, in launch_instance
    return super(JupyterApp, cls).launch_instance(argv=argv, **kwargs)
  File "/opt/jupyter/local/lib/python2.7/site-packages/traitlets/config/application.py", line 595, in launch_instance
    app.initialize(argv)
  File "<decorator-gen-7>", line 2, in initialize
  File "/opt/jupyter/local/lib/python2.7/site-packages/traitlets/config/application.py", line 74, in catch_config_error
    return method(app, *args, **kwargs)
  File "/opt/jupyter/local/lib/python2.7/site-packages/notebook/notebookapp.py", line 1021, in initialize
    self.init_configurables()
  File "/opt/jupyter/local/lib/python2.7/site-packages/notebook/notebookapp.py", line 815, in init_configurables
    connection_dir=self.runtime_dir,
  File "/opt/jupyter/local/lib/python2.7/site-packages/traitlets/traitlets.py", line 529, in __get__
    return self.get(obj, cls)
  File "/opt/jupyter/local/lib/python2.7/site-packages/traitlets/traitlets.py", line 508, in get
    value = self._validate(obj, dynamic_default())
  File "/opt/jupyter/local/lib/python2.7/site-packages/jupyter_core/application.py", line 99, in _runtime_dir_default
    ensure_dir_exists(rd, mode=0o700)
  File "/opt/jupyter/local/lib/python2.7/site-packages/ipython_genutils/path.py", line 167, in ensure_dir_exists
    os.makedirs(path, mode=mode)
  File "/opt/jupyter/lib/python2.7/os.py", line 157, in makedirs
    mkdir(name, mode)
OSError: [Errno 13] Permission denied: '/run/user/1000/jupyter'
```

I've never had this issue before, plus nobody in our team received any correspondence from IT regarding any changes. The server is indeed shared by me and another intern, though. One of my senior occasionally hops on the server, but she's not computing anything anyways. At first, I thought it was indeed a permission issue. Since everybody on the network is assigned an alias that's consists of numbers and alphabets, I can't tell which user the ID was referring to (in this case, **1000**). Of course, I didn't have sudo access on the server as well. Some posts suggest to check the I/O access on the Jupyter folder, but it belongs to me and not *root*. I felt kind of hopeless at that moment. Luckily, I got another error right before the `OSError`, so I used that as a keyword to search. Which brought me to [this issue](https://github.com/jupyter/notebook/issues/1318), my real life saver.

The discussion seems to suggest that the environment variable `$XDG_RUNTIME_DIR` was locked somehow. I don't think I was the one who initiated the process, or else I should NOT encounter this issue. It's weird that the environment variable didn't change even if I logged out and logged back in several times. But forcing my environment to forget `$XDG_RUNTIME_DIR` did help:

```
export XDG_RUNTIME_DIR=""
```

Just to play safe, I've also added `unset XDG_RUNTIME_DIR` to my `.bashrc` file. Then I got my Jupyter back! :P

I'm so happy so let me end with a cat photo. My princess is the best! Happy Friday, and hopefully I won't lose interests on this and keep updating :)

![_config.yml]({{ site.baseurl }}/images/wc1.jpg)
