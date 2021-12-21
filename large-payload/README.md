# Large payload

Building and deploying using `balena deploy --build` doesn't work for large services (such as ones with CUDA / NVIDIA drivers).


```
$ balena deploy h_gaiser/test --build
...
...
[Build]   <redacted> Image size: 472.05 MB
[Build]   <redacted> Image size: 12.96 GB
[Build]   <redacted> Image size: 282.49 MB
[Build]   <redacted> Image size: 528.12 MB
[Build]   <redacted> Image size: 991.64 MB
[Build]   <redacted> Image size: 2.82 GB
[Build]   <redacted> Image size: 14.36 GB
[Build]   <redacted> Image size: 14.21 GB
[Build]   <redacted> Image size: 2.64 GB
[Build]   <redacted> Image size: 931.36 MB
[Build]   Built 10 services in 2:37
[Info]    Creating release...
[Info]    Pushing images to registry...
[Info]    Saving release...
[Error]   Deploy failed
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Error</title>
</head>
<body>
<pre>Payload Too Large</pre>
</body>
</html>
