---
layout: post
title: When Tensorflow refuses to sit on your disk;
---

Yeah, it **DID** happen to me two weeks ago. I was about to mess with the [Mask RCNN](https://github.com/matterport/Mask_RCNN) package, but I couldn't get it to work. It has some technical requirements such as *Tensorflow >= 1.3* etc. I probably had a different version already installed under my virtual environment, and the GPU was compiled to use CUDA 8, not 9. So after numerous testing, I found that I need to get `Tensorflow==1.7.0`. Well... it shouldn't be that painful, `pip install` usually do the trick. But somehow I ran out of all the temp space on the VPS, so I kept on getting `OSError: no space left on ...`. It's so frustrated, I was panic and thought that I can't install any Python packages after all. But... no. Run the following command and it should save you. In fact, it's applicable to any large package, not only limited to Tensorflow - as long as it has a lot of temp files, try this so as to install it without the pain of cleaning up temp files later:

```
TMPDIR=/path/to/temp pip install tensorflow==1.7.0 --no-cache-dir
```

**BOOM!** Magic happens. Just make sure that you do have the directory `/path/to/temp` exists beforehand. Happy hacking!

PS: Two weeks later I'll be gone! My summer vacation finally, officially starts. A bit sentimental now :P but I miss my cats!!!
