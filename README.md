This is a sample of a basic service that is being monitored by prometheus and subsequently grafana.  I am assuming that you have the ability to run docker locally.

In order to run the sample locally, first clone the project down to your local environment

<code>git clone https://github.com/mjdavies/observability_example.git</code>

cd into the observability_example folder and start the applications

<code>cd observability_example && docker compose up -d</code>

Once completed you should now have 3 containers running, a fastapi python sample app, grafana and prometheus, "docker ps" should output something very similar to this

<code>ae3c25e6aa09   observability_example-api   "/start.sh"              23 seconds ago   Up 22 seconds   0.0.0.0:8000->8000/tcp, [::]:8000->8000/tcp   fastapi-application
858e549a66ea   grafana/grafana             "/run.sh"                23 seconds ago   Up 22 seconds   0.0.0.0:3000->3000/tcp, [::]:3000->3000/tcp   grafana
0ae411273b4f   prom/prometheus             "/bin/prometheus --c…"   23 seconds ago   Up 22 seconds   0.0.0.0:9090->9090/tcp, [::]:9090->9090/tcp   prometheus</code>

Prometheus (localhost:9090) is configured to scrape metrics from the sample fastapi python app, localhost:8000/metrics and also from itself, localhost:9090/metrics

Grafana (localhost:3000, username:admin, password:grafana) is configured to pull data from the two jobs, (prometheus and fastapisample).

Browse to localhost:3000 and login, click on Dashboards on the left menu
You'll see a fastapisample folder, with a fastapisample dashboard.  That dashbaord is checking the "up" metric coming from the fastapi application.

Click on Alert Rules on the left had menu
You'll see a fastapisample alert in the grafana-managed section. It should be showing as normal as the api is up, or reporting 1.

Back in terminal, stop the fastapi-application

<code>docker stop fastapi-application</code>

Browse back to the alert rules page in grafana and after a short period of time that will move into a firing state, and the dashboard will also display the value as being 0.
You'll also see it referenced in the active notifications section linked to in the left hand navigation.

I haven't configured any way of sending the alerts as of now, not sure that would be overkill for this piece of work, but it's very straightforward to do in a varied number of ways, slack, teams, etc etc, the normal gang of alert notifications.

The dashboards and alert rules are provisioned as files through the docker build process, so they can be versioned, and the same principle could be used to check a metric coming from any source, ie, CPU use or disk space alerts.


Technology Choices
Aside from being familiar with the tech involved

1. Used docker as it's quick and easy to get services/servers running locally you can see and play with
2. Used fastapi with it's prometheus client as you can see how to make custom metris easily if you need to from an app.
3. Used Prometheus as it's stable and a well contributed to project, as well as being a good fit for this demo/sample. Also I espcially like that you can pass configuration into it in files that can be kept in version control. No clicking please
4. Used Grafana for the same reason as I used Prometheus detailed above
5. Alerting mainly setup on Grafana as Prometheus needs alertmanger, whilst grafana can do it without an extra hop, plus you can use multiple sources for your alerts in grafana and its more feature rich. I would still setup alerting on prometheus to monitor grafana, and maybe act as a back up in case of a catastrophic failure of grafana.

   