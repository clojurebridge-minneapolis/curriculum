# Web App Notes


## Set up your app

```bash
lein new compojure chat
```


## Say hello to yourself

Change the ```(GET "/" ...``` line to say hello to yourself
```clojure
  (GET "/" [] "Hello YourName!")
```

View your page at http://localhost:3000/


## Write some html with Hiccup

Under the ```(GET "/" ...``` line, add a new route like this:
```clojure
  (GET "/hello2" [] (page/html5 [:div "Hello from " [:b "hiccup"] "!"]))
```

If you save the file, you'll get an error in your console because Clojure doesn't know what Hiccup is yet. We'll need to add hiccup to our ```project.clj``` file and require it in ```handler.clj```.
```clojure
; add this to the ```:dependencies``` section of project.clj
[hiccup "1.0.5"]

; add this to the ```:require``` section at the top of handler.clj
[hiccup.page :as page]
```

View your page at http://localhost:3000/


## Add a form

Add a new item in ```:require```
```clojure
[hiccup.form :as form]
```

Add a new route for rendering a form that calls a function to make the page
```clojure
; add a function (Note: this must be before the defroutes section
(defn who []
  (page/html5
    (form/form-to [:post "/iam"]
                  [:label "Who are you?"]
                  (form/text-field "name")
                  (form/submit-button "Submit"))))
; add a route
(GET "/who" [] (who))
```

View your page at http://localhost:3000/who. If you submit the form, you'll get an error, so we need to handle the post like this:
```clojure
; make a function to display our response
(defn iam [params]
  (page/html5
    [:div "You are " (:name params) "!"]))

; add a route to handle the request
(GET "/iam" {params :params} (iam params))
```


## Add some flow control

```clojure
; change the iam function to look like this
(defn iam [params]
  (page/html5
    [:div "You are " (:name params) "!"
     [:ul
      [:li "Your name is " (if (odd? (count (:name params)))
                             "odd"
                             "even")]
      (when (> (count (:name params)) 7)
          [:li "You should consider a nickname."])]]))
```


## Simplify things with ```let```

That's nice, but we've written ```(:name params)``` a lot. Change the function to use a ```var```:
```clojure
; change the iam function to look like this
(defn iam [params]
  (page/html5
    (let [name (:name params)]
      [:div "You are " name "!"
       [:ul
        [:li "Your name is " (if (odd? (count name))
                               "odd"
                               "even")]
        (when (> (count name) 7)
            [:li "You should consider a nickname."])]])))
```


## Maps

We can look at our params map by printing it out. Change the ```iam``` function to this:
```clojure
; change the iam function to look like this
(defn iam [params]
  (page/html5
    (let [name (:name params)]
      [:div "You are " name "!"
       [:ul
        [:li (str params)]
        [:li "Your name is " (if (odd? (count name))
                               "odd"
                               "even")]
        (when (> (count name) 7)
            [:li "You should consider a nickname."])]])))
```


## Post a name and message

```clojure
; change our default route to this
(ANY "/" {params :params} (chat (:name params) (:msg params))

; add a chat function
```clojure
(defn chat [name msg]
  (page/html5
   ;(map (fn [message] [:div [:strong (first message)] " " (second message)]) @messages)
   (form/form-to
    [:post "/"]
    [:div "Name:" (form/text-field "name" name) " Message:" (form/text-field "msg")]
    (form/submit-button "Submit"))))
```

View your app and submit a name and a message. Notice that the name is still there when you
submit the page.


## Store and display messages

First, add an atom so we can store our messages somewhere.
```clojure
; above the chat function
(def messages (atom []))
```

Next, update our messages atom and print them out
```clojure
(defn chat [name msg]
  (when-not (empty? msg)
    (swap! messages conj [name msg]))
  (page/html5
    (map (fn [message] [:div [:strong (first message)] " " (second message)]) @messages)
    (form/form-to
      ; leave the rest of the function the same as it was..
```

Try using your app to chat with someone


## Make it pretty by adding Bootstrap

Add Bootstrap to our project by adding this dependency in your ```project.clj``` file:
```clojure
[hiccup.bootstrap.page :as boot]
```

Add some code to make Bootstrap work
```clojure
; require Bootstrap at the top after the hiccup.form line
[hiccup.bootstrap.page :as boot]

; modify our chat function to use Bootstrap
(defn chat [name msg]
  ; leave this part the same
  (page/html5
   [:head
    [:title "Chat"]
    (boot/include-bootstrap)]
   [:body
    [:h1 "Chat"]
    [:div.well
     (map ...)] ; the code inside the 'map' call should remain unchanged
  ; leave the rest of the function the same

; add the Bootstrap specific routes by wrapping our routes with them
(def app
  (handler/site (wrap-bootstrap-resources app-routes)))
```
