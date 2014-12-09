GCM CCS (XMPP)
==============

[![Build Status](https://travis-ci.org/nbusy/gcm.svg?branch=master)](https://travis-ci.org/nbusy/gcm) [![GoDoc](https://godoc.org/github.com/nbusy/gcm?status.svg)](https://godoc.org/github.com/nbusy/gcm)

GCM (Google Cloud Messaging) CCS (Cloud Connection Server) implementation for application servers as described in [Android docs](https://developer.android.com/google/gcm/ccs.html).

Uses the XMPP endpoint to have persistent and asynchronous connection with the Google's GCM servers.

The HTTP implementation is a work in progress but it is advisable to use the CCS (XMPP) implementation instead as it is asynchronous and hence utilizes server resources more efficiently.

CCS Example
-----------

```go
package main

import (
	"log"

	"github.com/soygul/gcm/ccs"
)

func main() {
	c, err := ccs.Connect("gcm-preprod.googleapis.com:5236", "gcm_sender_id", "gcm_api_key", true)
	if err != nil {
		log.Fatalf("GCM CCS connection cannot be established.")
	}

	for {
		log.Printf("Waiting for incoming CCS messages")
		m, err := c.Receive()
		if err != nil {
			log.Printf("Incoming CCS error: %v\n", err)
		}

		go readHandler(m)
	}
}

func readHandler(m *ccs.InMsg) {
	log.Printf("Incoming CCS message: %v\n", m)
}
```

You can also use the `Conn.Send(...)` function to send messages to devices but it's not shown here as it requires a registration ID from a known device. Check the godocs for more information.

Testing
-------

All the tests can be executed by regular `go test` command while integration tests require the following environment variables to be defined. If they are missing, integration tests are skipped.

```bash
export GCM_CCS_HOST=gcm-preprod.googleapis.com:5236
export GCM_SENDER_ID=preprod_sender_id
export GOOGLE_API_KEY=preprod_api_key

export GCM_REG_ID=optional_reg_id_from_android_device
```

`GCM_REG_ID` is optional and it is a GCM registration ID taken from an Android device or simulator. If provided, it will be used in executing server-to-device messaging tests. Otherwise, these tests will be skipped.

License
-------

[MIT](LICENSE)
