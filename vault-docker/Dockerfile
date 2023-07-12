FROM debian:latest
RUN apt update
RUN apt install curl -y
RUN apt install unzip -y
RUN curl https://releases.hashicorp.com/vault/1.14.0/vault_1.14.0_linux_386.zip -o vault.zip && unzip vault.zip
RUN mv vault /bin/
RUN chmod -R 7777 /bin/vault
ENV VAULT_ADDR=http://127.0.0.1:8200
COPY . .
CMD [ "vault","server","-config=config/config.hcl"]