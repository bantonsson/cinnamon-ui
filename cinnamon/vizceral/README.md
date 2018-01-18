# Troubleshooting Vizceral

If you get stuck at the loading screen, there are a couple of things to check.

First, are all the right containers running.

```
> docker ps -a --format "{{.Names}}" | grep cinnamonsamplevzc
cinnamonsamplevzc_web_1
cinnamonsamplevzc_backend_1
cinnamonsamplevzc_elasticsearch_1
```

If any of the containers are not running, then CTRL-C the `docker-compose command` and make sure that all containers are stopped and removed.

```
> docker ps -a --format "{{.Names}}" | grep cinnamonsamplevzc | xargs docker stop
> docker ps -a --format "{{.Names}}" | grep cinnamonsamplevzc | xargs docker rm
```

Then restart `docker-compose up` and check again.

If all containers are running and you should run your application and try loading the vizceral page at `http://localhost:8080/`. If you still get stuck at the loading screen, then you need to add kibana to the docker compose file, to be able to check if your data go into elastic search. Shut everything down as explained above and add the following to the `docker-compose.yml` after the elasticsearch entry:

```
  kibana:
    image: docker.elastic.co/kibana/kibana:5.6.3
    ports:
      - "5601:5601"
    networks:
      - cinnamon
    depends_on:
      - elasticsearch
```

Start `docker-compose up`, and surf to `http://localhost:5601` and log in with the default user/password, `elastic/changeme`. Then in the index pattern box, type `cinnamon-metrics-YYYY-MM`, where `YYYY` is the current year and `MM` is the current month. If metrics have been reported into elastic search, then you should be able to use the *Discover* tab to view the metrics. Otherwise you will be stuck at welcome screen with the error message _"Unable to fetch mapping. Do you have indices matching the pattern?"_.
