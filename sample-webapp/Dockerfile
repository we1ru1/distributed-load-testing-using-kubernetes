FROM python:3.9
RUN pip install Flask -i http://pypi.douban.com/simple&& \
pip install gunicorn -i http://pypi.douban.com/simple
COPY app.py /root/
COPY app.yaml /root/
WORKDIR /root/
# Replace this line with a realistic test script.
ENTRYPOINT ["gunicorn", "-b 0.0.0.0:8000", "app:app"]
