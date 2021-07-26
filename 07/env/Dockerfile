FROM ubuntu:latest
RUN apt-get update && apt-get -y install fortune
ADD fortuneloop-env.sh /bin/fortuneloop-env.sh
ENTRYPOINT ["/bin/fortuneloop-env.sh"]
CMD ["10"]