Postal
=======

#### Internet email library for Clojure

Postal is a library for constructing and sending RFC822-compliant
Internet email messages.  It wraps the JavaMail package for message
and SMTP support.  It supports sendmail natively.

### Platforms

* Any Unix platform that supports Java and sendmail (`msmtp`, et al.)
* Untested on Windows

### Dependencies

* JavaMail 1.4.2 (in `lib/` after build)

### Examples

At a bare minimum, provide a map with `:from`, `:to`, `:subject`, and `:body`.
This will locally inject the message into sendmail.

    user> (in-ns 'com.draines.postal.core)
    #<Namespace com.draines.postal.core>
    com.draines.postal.core> (send-message {:from "me@draines.com"
                                            :to ["mom@example.com" "dad@example.com"]
                                            :cc "bob@example.com"
                                            :subject "Hi!"
                                            :body "Test."})
    {:code 0, :error :SUCCESS, :message "message sent"}
    com.draines.postal.core> 

To use SMTP, add metadata with a `:host` key.

    com.draines.postal.core> (send-message #^{:host "mail.isp.net"}
                                           {:from "me@draines.com"
                                            :to "foo@example.com"
                                            :subject "Hi!"
                                            :body "Test."})
    {:code 0, :error :SUCCESS, :message "message sent"}
    com.draines.postal.core> 

Attachments and multipart messages can be added as sequences of maps:

    com.draines.postal.core> (send-message #^{:host "mail.isp.net"}
                                           {:from "me@draines.com"
                                            :to "foo@example.com"
                                            :subject "Hi!"
                                            :body [{:type "text/html"
                                                    :content "<b>Test!</b>"}
                                                ;;;; supports both dispositions:
                                                   {:type :attachment
                                                    :content (java.io.File. "/tmp/foo.txt")}
                                                   {:type :inline
                                                    :content (java.io.File. "/tmp/foo.txt")}]})
    {:code 0, :error :SUCCESS, :message "message sent"}
    com.draines.postal.core>

You can stress-test a server by:

    com.draines.postal.core> (stress #^{:host "localhost"
                                        :num     1000
                                        :delay   250   ;; msecs
                                        :threads 5     ;; concurrent connections}
                                     {:from "foo@lolz.dom"
                                      :to "bar@lolz.dom"})
    sent 1000 msgs to localhost:25
    nil
    com.draines.postal.core>


### Building

    % lein deps && lein jar


## License

Postal is (c) 2009 Drew Raines and released under the MIT license.
