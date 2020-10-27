# Woo

[![Build Status](https://travis-ci.org/fukamachi/woo.svg?branch=master)](https://travis-ci.org/fukamachi/woo)

Woo is a fast non-blocking HTTP server built on top of [libev](http://software.schmorp.de/pkg/libev.html). Although Woo is written in Common Lisp, it aims to be the fastest web server written in any programming language.

# Need
* libev
* quicklisp

# Use

---

```common-lisp
(ql:quickload :woo)

(defun app (env)
 `(200 nil ("hello, world")))
 
(woo:run #'app)

```

---

```common-lisp
(ql:quickload :woo)

(defun app (env)
 `(200 (:Content-Type "text/plain; charset=utf-8" :X-power-by "common-lisp") ("hello, world")))
 
(woo:run #'app :debug nil :port 8080 :address "127.0.0.1") 

```

---

```common-lisp
(ql:quickload :woo)

(defun app (env)
 `(200 (:Content-Type "text/html; charset=utf-8" :Server "wooo") ("<p>hello<\/p>")))
 
(sb-thread:make-thread 
  (lambda () 
    (woo:run #'app :debug nil :port 8080 :address "0.0.0.0" :worker-num 6)))
    
(defun app (env)
 `(200 (:Content-Type "text/html; charset=utf-8" :X-author "linnilbobo") "/the/path/to/your/html/file.html"))
 
(defun app (env)
 `(200 (:Content-Type "image/png" :X-Content-Type-Options "nosniff") #(the-file-to-the-unsigned-octet-vector)))
 
(defun app (env)
  (let ((the-request-method (getf env :REQUEST-METHOD)))
    (if (or (eq the-request-method :get) (eq the-request-method :post))
      `(200 nil ("ok"))
      `(404 nil ("not ok")))))
  
(defun app (env)
  (let ((the-path-info (getf env :path-info)))
    `(200 nil ,(format nil "i know you want to see ~A" the-path-info))))
    
(defparameter *the-path-map* 
  (make-hash-table 
		    :test #'equal
		    :size 100
                    :rehash-size 100
                    :rehash-threshold 0.8
                    :weakness nil
                    :synchronized nil))
                    
(setf (gethash "/" *the-path-map*) `(200 (:Content-Type "text/html; charset=utf-8") "the-path-to-index-html-file.html"))
;(set (gethash "/look" ...
;use uiop to get all the file name of directory

(defun app (env)
  (let ((the-path-info (getf env :path-info)))
  
    (let ((get-the-path (gethash the-path-info *the-path-map*)))
      (if get-the-path
          get-the-path
          `(404 nil ("page-not-found"))))))

;and woo do not support ssl, so you need a REVERSE PROXY, not if you don’t need https

```

---

## Author

* Eitaro Fukamachi (e.arrows@gmail.com)
* Documents: linnilbobo (if some thing wrong, please tell me)

## Copyright

Copyright (c) 2014 Eitaro Fukamachi & [contributors](https://github.com/fukamachi/woo/graphs/contributors)

## License

Licensed under the MIT License.
