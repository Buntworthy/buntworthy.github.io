---
layout: post
title: Read VisualSFM NVM file
published: true
---

[VisualSFM](http://ccwu.me/vsfm/) is the fastest easiest way I've seen to try out [structure from motion](http://en.wikipedia.org/wiki/Structure_from_motion). (I'm always amazed that this is even possible!). VisualSFM produces .NVM files which contain all the 3D points (as well as colours) and the camera matrices. Here is a Matlab 'readNVM' function I wrote at some point to read the NVM file into Matlab variables.

{% highlight matlab %}
function [camera point] = readNVM(filename)
fid = fopen(filename);
c = textscan(fid,'%s');

if strcmp(c{1}(2),'FixedK')
    offset = 9;
    numCameras = str2double(c{1}(8));
else
    offset = 3;
    numCameras = str2double(c{1}(2));
end
%% extract the cameras and their parameters
for iCam = 1:numCameras
    camera{iCam}.name = c{1}(11*(iCam-1) + offset);
    disp(['Reading camera ',camera{iCam}.name])
    camera{iCam}.f = str2double(c{1}(11*(iCam-1) + offset + 1));
    camera{iCam}.rot = str2double(c{1}(11*(iCam-1) + offset + 2:11*(iCam-1) + offset + 5));
    camera{iCam}.trans = str2double(c{1}(11*(iCam-1) + offset + 6:11*(iCam-1) + offset + 8));
    camera{iCam}.distortion = str2double(c{1}(11*(iCam-1) + offset + 9));
end

%% extract the point features
idx = numCameras*11 + offset + 1; % starting index of the point features
count = 1;
data = str2double(c{1});
while idx < length(c{1})-42
    % read in initial data about point
    disp([num2str(idx/(length(c{1})-42)*100),' percent complete'])
    point(count).pos = (data(idx:idx+2));
    point(count).col = (data(idx+3:idx+5));
    point(count).nMeasurements = (data(idx+6));
    idx = idx + 7;
    % read in image locations of point
    for iMeasurement = 1:point(count).nMeasurements
        point(count).measurement(iMeasurement).image =  (data(idx));
        point(count).measurement(iMeasurement).feature =  (data(idx+1));
        point(count).measurement(iMeasurement).imPos =  (data(idx+2:idx+3));
        idx = idx + 4;
    end
    % increment counter
    count = count + 1;
end
{% endhighlight %}