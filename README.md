# Quarkus Bucket4j

[![Version](https://img.shields.io/maven-central/v/io.quarkiverse.bucket4j/quarkus-bucket4j?logo=apache-maven&style=flat-square)](https://search.maven.org/artifact/io.quarkiverse.bucket4j/quarkus-bucket4j)

Bucket4J is a Java rate-limiting library based on the token-bucket algorithm. Bucket4j is a thread-safe library that can be used in either a standalone JVM application, or a clustered environment. It also supports in-memory or distributed caching via the JCache (JSR107) specification.
This extension allow you to control the request rate sent to your application by using a dead simple API.

## Getting Started

Read the full documentation [TODO].

### Usage

Annotate the method that need to be throttled with @RateLimited

``` java
@ApplicationScoped
public static class RateLimitedMethods {

    @RateLimited(bucket = "group1")
    public String limited() {
        return "LIMITED";
    }

}
```

And add a limit group using the same limitsKey in the configuration:

``` properties
# burst protection
quarkus.rate-limiter.buckets.group1[0].permitted-uses: 10
quarkus.rate-limiter.buckets.group1[0].period: 1S
# fair use
quarkus.rate-limiter.buckets.group1[1].permitted-uses: 100
quarkus.rate-limiter.buckets.group1[1].period: 5M
```

The limit group can contain multiple limit that will all be enforced.

If you want to enable throttling per user, simply specify an IdentityKeyResolver in the RateLimited annotation

``` java
@ApplicationScoped
public static class RateLimitedMethods {

    @RateLimited(bucket = "group1", identityResolver = IpResolver.class)
    public String limitedByIp() {
        return "LIMITED";
    }
}
```

IpResolver is provided out of the box. if you want a more complex segmentation, you can implement your own resolver.
A custom resolver must be a valid CDI Bean.