FROM python:2.7.18-slim-stretch

WORKDIR /

COPY ubootwrite.py .

RUN chmod a+x ubootwrite.py
RUN pip install serial

ENTRYPOINT [ "/ubootwrite.py" ]
CMD [ "-h" ]