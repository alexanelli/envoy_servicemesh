## Serice Mesh with Envoy
![setup](./envoy_servicemesh.png)

---
### **Ports/Networking**

### Service A

#### Go App
calling service b: `http://service_a_envoy:8281/`

calling service c: `http://service_a_envoy:8381/`

listenandserve on: `http://localhost:8081`

#### Envoy Config
static_resources.listeners
- service-a-svc-http-listener (ingress)
```Yaml
address: 0.0.0.0
port: 8181
```

- service-b-svc-http-listener (egress to service b)
```Yaml
address: 0.0.0.0
port: 8281
```

- service-c-svc-http-listener (egress to service c)
```Yaml
address: 0.0.0.0
port: 8381
```

clusters
service_a.hosts.socket_address (service a to its own envoy)
```Yaml
address: service_a
port: 8081
```
service_b.hosts.socket_address (service a to b's envoy)
```Yaml
address: service_b_envoy
port: 8182
```
service_c.hosts.socket_address (service a to c's envoy)
```Yaml
address: service_c_envoy
port: 8183
```

#### Docker
service_a_envoy: `8181:8181`
service_a: `8081:8081`

---

### Service B

#### Go App
listenandserve on: `http://localhost:8082`

#### Envoy Config
static_resources.listeners (ingress)
- service-b-svc-http-listener
```Yaml
address: 0.0.0.0
port: 8182
```

clusters.service_b.hosts.socket_address
```Yaml
- address: service_c
- port_value: 8082
```


#### Docker
service_b.ports:
- 8082:8082

---

### Service C

#### Go App
listenandserve on: `http://localhost:8083`

#### Envoy Config
static_resources.listeners
- service-b-svc-http-listener (ingress)
```Yaml
address: 0.0.0.0
port: 8183
```

clusters.service_c.hosts.socket_address
```Yaml
- address: service_c
- port_value: 8083
```

#### Docker
service_c.ports:
- 8082:8082

---

### Run:
1. `docker-compose build`
2. `docker-compose up`
3. Hit http://localhost:8080

~4. Visit http://localhost:9090 for prometheus~ (not enabled right now)

~5. Visit http://localhost:3000 for grafana dashboard~ (not enabled right now)
