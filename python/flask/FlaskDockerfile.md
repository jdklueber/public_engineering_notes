# Flask in Docker

```dockerfile
FROM python:3.10
EXPOSE 5000
WORKDIR /app
RUN pip install flask
COPY . .
CMD["flask", "run", "--host", "0.0.0.0"]
```

## Notes

1. Use the latest python version 
1. Set the port as appropriate

5. Here, the first `.` is the current directory on your machine and the second `.` is the `WORKDIR` set in line 3



