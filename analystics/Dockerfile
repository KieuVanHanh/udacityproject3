FROM public.ecr.aws/docker/library/python:3.10-slim-buster
# Set environment variables
ENV APP_PORT=5153
ENV DB_USERNAME=default_username
ENV DB_PASSWORD=default_password
ENV DB_HOST=127.0.0.1
ENV DB_PORT=5432
ENV DB_NAME=postgres

WORKDIR /src

COPY ./app/requirements.txt requirements.txt

RUN pip install -r requirements.txt

COPY ./app .

EXPOSE 5153

CMD python app.py