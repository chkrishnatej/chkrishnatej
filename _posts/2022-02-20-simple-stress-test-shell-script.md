---
title: Simple Stress Test for REST API
author:
  name: Krishna Tej
  link: https://github.com/chkrishnatej
date: 2021-02-20 22:15:00 +0530
categories: [Codes, Shell]
tags: [code-recipes]
---
## Why is it needed?
As a part of my workflow, I had to repeatedly hit the same REST API end points to simulate load on the system. A shell script works better and is a leaner solution on most Unix systems.

This is a simple script which allows you to do that with number of time stress test has to be done. The repetition is provided as an argument.

## Usage Matrix

Spec | Value
--- | ---
Operating System | Mac
Shell | `zsh 5.8`
Curl Version | 7.77.0

## Regular usage

### Script

```zsh
#!/bin/zsh

echo "ZSH version ${ZSH_VERSION}..."
for i in {1..${1:-1}}
  do 
    echo "Hitting the end point for ${i} time"
    curl -X GET "https://httpbin.org/get" -H "accept: application/json"
  done
``` 

### Sample Response

```json
{
  "args": {}, 
  "headers": {
    "Accept": "application/json", 
    "Host": "httpbin.org", 
    "User-Agent": "curl/7.77.0", 
    "X-Amzn-Trace-Id": "Root=1-6216f671-5be6917f342bba8e1610fb04"
  }, 
  "origin": "1.1.1.1",
  "url": "https://httpbin.org/get"
}
```

### Usage

#### Normal usage
By default the `curl` command hits the API endpoint only once
```console
./load-it.sh
```

#### Using for stress test
An argument of `int` type can be provided of your choice. 

The script runs **100** times <br>
```console
./load-it.sh 100
```
--- 

## Getting some basic metrics for each API call

The above `curl` command is good for testing response of the API endpoints. To get some extra info out of each API call, you can format the `curl` response format. 

To format the response with some additional information, create a file with format as `*.txt`.

### Creating `curl` format file

Create a file called `curl-format.txt`

```text
{\n
  "metrics" : {\n
                "time_total": %{time_total}s,\n
                "size_upload": %{size_upload} bytes,\n
                "size_download": %{size_download} bytes,\n
              }\n
}\n
```
{: .nolineno }

The file represents a stringified JSON using delimiters like space and `\n` for formatting.

### Updating the script

```shell
#!/bin/zsh

echo "ZSH version ${ZSH_VERSION}..."
for i in {0..$1}
  do 
    echo "Hitting the end point for ${i} time"
    curl -w "@curl-format.txt" -X GET "https://httpbin.org/get" -H "accept: application/json"
    echo -e  "---\n\n\n"
  done
```

### Usage
The usage remains the same as before. Refer to [Usage](/posts/simple-stress-test-shell-script/#usage)

### Sample Response

```json
{
  "args": {}, 
  "headers": {
    "Accept": "application/json", 
    "Host": "httpbin.org", 
    "User-Agent": "curl/7.77.0", 
    "X-Amzn-Trace-Id": "Root=1-62172079-6715cf5b1b79eb633684ab84"
  }, 
  "origin": "1.1.1.1", 
  "url": "https://httpbin.org/get"
}
{
  "metrics" : {
                "time_total": "1.028342s",
                "size_upload": "0 bytes",
                "size_download": "268 bytes",
              }
}
```

## References
* [Curl Write Out Man Page](https://curl.se/docs/manpage.html#:~:text=%2Dw%2C%20%2D%2Dwrite%2Dout%20%3Cformat%3E)