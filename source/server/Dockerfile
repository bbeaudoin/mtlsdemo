FROM python:alpine

RUN mkdir /src

# Gunicorn configuration
ADD gunicorn.conf /src
COPY root.crt /usr/local/share/ca-certificates/
RUN update-ca-certificates

# Flask app
ADD server.py /src
ADD requirements.txt /src
RUN pip3 install -r /src/requirements.txt

# Expose port 8443
EXPOSE 8443

# app, certificate watcher and envoy
CMD ["gunicorn", "--config", "/src/gunicorn.conf", "--pythonpath", "/src", "server:app"]
