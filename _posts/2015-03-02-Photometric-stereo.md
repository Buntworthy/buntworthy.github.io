---
layout: post
title: Photometric stereo
published: true
---

[Photometric stereo](http://en.wikipedia.org/wiki/Photometric_stereo) is a technique to reconstruct surface normals and appearance from a series of photographs taken from the same position, but under varying lighting conditions. Despite seeming to work very well, and having been around since the 1980s it dosen't seem like it has seen a huge amount of popularity, at least not compared to Structure from Motion or Stereoscopic depth estimation (maybe that's because Photometric stereo seems like a bit of a misnomer).

So from three (simulated) images of a surface like these:
![Simulated intensity images](/images/2015-03-02_intensity.jpg)

You can pretty easily recover the normal map and albedo of the surface:
![Recovered normal map](/images/2015-03-02_normal.jpg)
![Recovered albedo](/images/2015-03-02_albedo.jpg)

And then it's a case of estimating the integral of the (noisy and unintegrable) normal map to recover the surface height. There's a [Matlab function on the file exchange](http://uk.mathworks.com/matlabcentral/fileexchange/43149-surface-reconstruction-from-gradient-fields--grad2surf-version-1-0) for this, but I haven't found anything to make my life easier for Python yet.