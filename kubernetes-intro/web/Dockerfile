FROM python:latest
RUN mkdir /app
WORKDIR /app
COPY homework.html /app
EXPOSE 8000
USER 1001
CMD python -m http.server 8000