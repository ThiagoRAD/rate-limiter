# Rate Limiter

to create a rate limiter, first you create a Connector, an example is at the main.go
```
	rateLimiter := r.NewRateLimiterConnector(r.REDIS)
```
the limiter works either in memory or using redis. Then after this you can create a default Rate at the rate limiter, and special rates (that use api keys)

```
defaultRate := r.RequestRate{Requests: requests, Per: per, BlockDuration: blockDuration}
rateLimiter.SetDefaultRate(defaultRate)

specialRate := r.RequestRate{Requests: specialRequests, Per: specialPer, BlockDuration: specialBlockDuration}
rateLimiter.AddApiKey("123abc", specialRate)
```

Then you register the ServeMux at the RateLimiter, as follows

```
mux := http.NewServeMux()
mux.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
  fmt.Fprintln(w, "Hello, World!")
})

wrappedMux := rateLimiter.RegisterMux(mux)

http.ListenAndServe(":8080", wrappedMux)
```
