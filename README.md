# Viboo challenge - Temperature Monitoring API

## Overview
This project is a RESTful API designed to handle and analyze temperature data for multiple buildings and rooms. It provides endpoints to record temperature data and retrieve the average temperature over a specified duration. The application uses Flask as the backend framework and InfluxDB as the time-series database.

## Technologies Used
- **Backend Framework**: Flask
- **Database**: InfluxDB (Time-Series Database)
- **Language**: Python, version 3.12
- **Containerization**: Docker & Kubernetes

## Design decisions
### Database
For the database I decided to use a time-series database (InfluxDB) because it 
is optimised to deal with time-stamped data (e.g. sensor data, metrics). It is
optimised for writing and scales very well both vertically and horizontally.
Also, the timeseries databases it handles high volume data alongside 
igh-frequency writes which i expected from such application (e.g. many sensors
writing to the recorded temperature).

### Backend
For the backend I chose to use Flask because it allows a developer fast
development speed, but also having a lot of functionality out of the box.
It is also popular and it has great support through its extensions.


## Run

### Requirements
To run the project you need to have `Docker` and `docker-compose` installed. 

### Setup
To start the project simply run the command. This will build the project and deploy the influxDB and the flask backend application.

`docker compose up -d --build`

### Testing
For testing you can use `Postman` to send requests to the backend.
Another option is to use `curl` from terminal. Below there are a few examples:

- Posting a temperature reading
```
curl -X POST http://localhost:5000/api/v1/buildings/viboo/rooms/meeting-room-1/temperatures \
    -H "Content-Type: application/json" \
    -d '{
        "temperature": 10.5,
        "recorded_at": "2024-12-10T16:45:00"
    }'
```

- Getting the average for the last 15 minutes
```
curl -X GET "http://localhost:5000/api/v1/buildings/building1/rooms/room1/temperatures/average?duration=15m"
```

## Deployment
For the deployment of the application to a cloud environment the following steps must be done (steps 3 to last are not implemented).
1. Build the docker image for the backend application via the provided Dockerfile.
2. Push the image to a container registry (DockerHub)
3. Set up kubernetes secrets for the application like database user and password and the access token.
4. Set up persistence volumes and persistence volume claims for the database.
5. Set up database deployment via configuration and service yaml.
6. Set up the backend app deployment via configuration and service yaml.
7. Verify the deployment on kubernetes: check the liveness of each pod, check the interactions between the pods.

## CI/CD outline
We can breakdown the CI/CD pipeline into the following steps:
1.  Linting and Static Analysis - run flake and black for style checks and code format.
2. Testing - create an action to run unit, integration and regression tests.
3. Build and Docker image - build the docker image using the Dockerfile from the repo and tag it with the current commit SHA.
4. Deployment - deploy the image to a cloud platform, preferably via the kubernetes engine of the cloud platform. Ideally we would have a develop and production environment such that we could run some tests on the develop environment (e.g. smoke, integration tests) before deploying to production.


## Future Enhancements
The project is still in its bare state and can be improved by the following enhancements:

- Protect the data, by only allowing authenticated user to post temperature readings.
- Add client caching in the header for the GET request.
- Implement user authentication for API endpoints.