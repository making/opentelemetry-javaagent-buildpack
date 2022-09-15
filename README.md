# `ghcr.io/making/buildpacks/opentelemetry-javaagent`

⚠️ This project will be no longer maintained after paketo buildpack for opentelemetry is released.<br>
**Use https://github.com/paketo-buildpacks/opentelemetry instead when it's released. **


The OpenTelemetry Java Agent Buildpack is a Cloud Native Buildpack that contributes the OpenTelemetry Java Agent.

## Behavior

This buildpack will participate if all the following conditions are met


* the build plan contains `jvm-application`
* The `$BP_OPENTELEMETRY_ENABLED` is set to `true` (**the breaking change since 0.2.0**)

The buildpack will do the following:

* Contributes a Java agent to a layer and configures `JAVA_TOOL_OPTIONS` to use it

This buildpack does not configure Java system properties (`-D` flags) or as environment variables for the agent. 
See [the configuration documentation](https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/main/docs/agent-config.md) for the full list of configuration items. 


## Example

```
git clone https://github.com/making/hello-servlet.git
pack build hello-servlet \
  --path hello-servlet \
  -e BP_OPENTELEMETRY_ENABLED=true \
  --buildpack paketo-buildpacks/java \
  --buildpack ghcr.io/making/buildpacks/opentelemetry-javaagent \
  --builder paketobuildpacks/builder:base
```

```
docker run \
  --rm \
  -p 9411:9411 \
  openzipkin/zipkin-slim

docker run \
  --rm \
  -p 8080:8080 \
  -e JAVA_TOOL_OPTIONS="-Dotel.resource.attributes=service.name=hello-service -Dotel.traces.exporter=zipkin -Dotel.exporter.zipkin.endpoint=http://host.docker.internal:9411/api/v2/spans" \
  hello-servlet
```

```
curl http://localhost:8080
```


Go to http://localhost:9411
![image](https://user-images.githubusercontent.com/106908/138395273-d58e2ec7-247d-4976-b7c2-7267fd879617.png)


## License
This buildpack is released under version 2.0 of the [Apache License](http://www.apache.org/licenses/LICENSE-2.0).
