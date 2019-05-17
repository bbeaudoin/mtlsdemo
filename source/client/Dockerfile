FROM python:alpine

RUN mkdir /src

ADD client.py /src
ADD requirements.txt /src
RUN pip3 install -r /src/requirements.txt

CMD ["python", "/src/client.py"]
