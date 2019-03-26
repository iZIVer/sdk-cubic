---
title: "Setup Connection"
weight: 10
---

Once you have your Cubic server up and running, let's see how we can use the SDK
to connect to it.

<!--more-->

First, you need to know the address (`host`:`port`) where the server is running.
This document will assume the values `127.0.0.1:2727`, but be sure to change
those to point to your server instance.  Port 2727 is the default GRPC port that
Cubic server binds to.

The following code snipped connects to the server and queries its version.  This
(default) connection expects the server to be listening on a TLS encrypted
connection.

{{%tabs %}}
{{% tab "Go" %}}
```go
package main

import (
	"context"
	"fmt"
	"log"

	"github.com/cobaltspeech/sdk-cubic/grpc/go-cubic"
)

const serverAddr = "127.0.0.1:2727"

func main() {
	client, err := cubic.NewClient(serverAddr)
	if err != nil {
		log.Fatal(err)
	}

	defer client.Close()

	version, err := client.Version(context.Background())
	if err != nil {
		log.Fatal(err)
	}

	fmt.Println(version)
}

```
{{% /tab %}}
{{%/tabs %}}


## Insecure Connection

It is sometimes require to connect to Cubic server without TLS enabled, such as
during debugging. 

Please note that if the server has TLS enabled, attempting to connect with an
insecure client will fail.
To connect to such an instance of cubic server, you can use:

{{%tabs %}}
{{% tab "Go" %}}
``` go
client, err := cubic.NewClient(serverAddr, cubic.WithInsecure())
```
{{% /tab %}}
{{%/tabs %}}

## Client Authentication

In our recommended default setup, TLS is enabled in the gRPC setup, and when
connecting to the server, clients validate the server's SSL certificate to make
sure they are talking to the right party.  This is similar to how "https"
connections work in web browsers.

In some setups, it may be desired that the server should also validate clients
connecting to it and only respond to the ones it can verify. If your Cubic
server is configured to do client authentication, you will need to present the
appropriate certificate and key when connecting to it. 

Please note that in the client-authentication mode, the client will still also
verify the server's certificate, and therefore this setup uses mutually
authenticated TLS. This can be done with:

{{%tabs %}}
{{% tab "Go" %}}
``` go
client, err := cubic.NewClient(serverAddr,  cubic.WithClientCert(certPem, keyPem))
```
{{% /tab %}}
{{%/tabs %}}

where certPem and keyPem are the bytes of the client certificate and key
provided to you.
