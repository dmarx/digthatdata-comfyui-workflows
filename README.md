# DigThatData's Advanced ComfyUI Tips and Tricks!

Apparently I've been attempting to share workflows on social platforms that were cleaning the metadata from the images! Whoops!

My goal here is mainly to encapsulate specific tips and tricks that can be incorporated in larger workflows, rather than publishing standalone, "do everything" workflows. 

The reason for this is that... well, that's just not how I use comfyui. I'll usually start from a base "simple" workflow, and build up a piece as I interact with it. My process tends to be very organic in this way, so by the time I'm finally happy with a piece: it probably has its own unique workflow. Rather than turning this into a gallery to show off how much effort goes into each piece, I'm going to try to use this space mainly to share more "atomic" techniques that I sometimes use.

To use the techniques described here, the easiest approach is probably to open up a **second** comfyui browser tab, load the my workflow you're interested in there (i.e. drag the png image onto the comfyui workspace), select and copy the nodes that comprise the trick, then go back to your main comfy tab and paste that chunk of the my workflow into your workspace.

## My "Base" Workflow

![base workflow](workflows/base-workflow.png)

This is usually what I start with. The left hand side is a normal text-to-image workflow that generates a batch of images. I use that space to workshop my prompt and other core conditioning and aesthetics. The right hand side is a sequence of AnimateDiff modules that are each disconnected from their respective "latent" inputs. When I'm happy with my prompt and ready to move on to animating, I just close the connection between the reroute nodes to activate the next AD module. Alternatively, if I want to add one of my "secret sauce" tricks, i can copy the nodes in one of the blue groups and add that module in-line to intercept the latents/images before passing them along to the next AD module. Did I mention I generally apply AD multiple times? I usually run at least two or three passes of AD when I use it.

The "bus" nodes used here aren't an intrinsic part of the workflow, I just find them useful for keeping my stuff organized. 

## Loop-Friendly Video Frame Interpolation (VFI)

![base workflow](workflows/loop-friendly-vfi.png)

A spectacular trick you can use to make smoother animations is to use frame interpolation (and/or slow down the frame rate in tandem with using VFI to elongate your frame count). I enjoy making short animated loops, which doesn't work well with this approach. Let's say we want to use VFI to interpolate 4 new frames between every frame we generated. Let's say we have a three frame animation, comprised of frames A, B and C. After VFI, we'll have 11 frames: A <4x VFI> B <4xVFI> C. The issue here is that we didn't add frames interpolating from C back to A, so when the video loops it'll seem to accelerate or "jerk" at the loop point. To fix this, we can concatenate the first frame on to the then giving us the sequence ABCA, perform our interpolation like before, and then just pop off either of the redundant duplicated frames of the animation. Rather than 11 frames, well end up with 15 frames and a smooth loop with no jerkiness from changing frame rates.
