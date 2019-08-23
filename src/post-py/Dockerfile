FROM python:3.6.0-alpine

WORKDIR /app
ADD . /app

RUN apk add --update gcc python python-dev py-pip build-base
RUN pip install -r /app/requirements.txt

ENV POST_DATABASE_HOST post_db
ENV POST_DATABASE posts

ENTRYPOINT ["python3", "post_app.py"]
