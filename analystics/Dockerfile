FROM public.ecr.aws/docker/library/python:3.10-slim-buster
WORKDIR /src

COPY ./requirements.txt requirements.txt

RUN pip install -r requirements.txt

COPY . .

CMD python app.py

