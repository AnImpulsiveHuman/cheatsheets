# Helmet Js
```
app.use(helmet())                                     -> sets all the headers to ensure best practices.
app.use(helmet.hidePoweredBy())                       -> hides the x-powered-by header
app.use(helmet.hidePoweredBy({ setTo: 'PHP 4.2.0' })) -> Changes the x-powered-by header to the specified text
app.use(helmet.frameguard({action: 'deny'}));         -> sets x-frame-options header (deny,sameorigin)
app.use(helmet.xssFilter());                          -> enables xss filter
app.use(helmet.noSniff()));                           -> sets x-content-type to nosniff to prevent bypassing content-type header
app.use(helmet.ieNoOpen());                           -> sets x-download-options to noopen (in internet explorer) to prevent auto opening of files after downloading
app.use(helmet.hsts({maxAge: 7776000, force: true}))  -> sets Strict-Transport-Security header, force: overides the default header
app.use(helmet.dnsPrefetchControl());                 -> turns off dns prefetch.
app.use(helmet.noCache());                            -> makes users load the site always by disabling caching on the users browser
app.use(helmet.contentSecurityPolicy({ directives: { defaultSrc: ["'self'"], scriptSrc: ["'self'", "trusted-cdn.com"] }} ));  -> conifgure CSP 
```
