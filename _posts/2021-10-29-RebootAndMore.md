---
layout: post
title:  "Reboot and more"
date:   2021-10-29
categories: jekyll update
---

### Reboot
While the design is *working*, the fact that windows are created before being assigned to a parent have become untenable.
This design is depleted, a new design must take its place.

Before that, let's build an assertion system in order to react to any error code returned from any system/API call.
While the native assert work, it does it allow to format to summarize the file, function, line number, expression and API specific error description.
It also does not allow to send this info to the console, to a file or to a message box.

### Assert
With the previous code out of the way safely tucked into the main branch, the assertion system is operational.

It supports acquiring detailed error information from
- Generic errors with no detailed information
- C library errors with *_tcserror_s*
- STL errors with *std::error_code*
- Win32 API errors with *GetLastError / FormatMessage*

The detailed error information is sent to the standard error stream by default, but can also be redirected to a file.

Missing features are
- Open a console and redirect stderr to it
- Redirect to a message box

#### Ref e0a642da8dd92bafc810ea78e162dab337b9ad97
