FROM python:3 as dev
ENV PYTHONUNBUFFERED 1

# # 環境変数を指定
# ARG USERNAME=dev
# ARG GROUPNAME=dev
# ARG UID=1000
# ARG GID=1000

RUN mkdir /home/${USERNAME} && \
    mkdir /home/${USERNAME}/backend
WORKDIR /home/${USERNAME}/backend


RUN apt-get update && apt-get install -y sudo && \
    echo "node ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
RUN apt-get install postgresql postgresql-contrib -y

# ユーザー作成
RUN groupadd -g ${GID} ${GROUPNAME} && \
    useradd -l -m -s /bin/bash -u ${UID} -g ${GID} ${USERNAME}

# 権限を保持した状態でコピー
COPY --chown=${USERNAME}:${GROUPNAME} backend/projects/requirements.txt /home/${USERNAME}/backend

RUN pip install --upgrade --no-cache-dir pip

# permission関係
RUN chown ${UID}:${GID} /home/${USERNAME} && \
    chmod 777 /home/${USERNAME} 
    
# django-admin start appのpathが通っていないため、追加。
ENV PYTHONUSERBASE=/home/$USERNAME/.local
ENV PATH=$PYTHONUSERBASE/bin:$PATH

USER ${USERNAME}

FROM dev as test

COPY backend/projects .
RUN pip install --retries 5 --upgrade --no-cache-dir -r requirements.txt

# RUN python manage.py makemigrations
# RUN python manage.py migrate

# CMD [ "gunicorn","--bind", ":8000", "Myapp.wsgi:application" ]
CMD [ "python","manage.py", "runserver", "0.0.0.0:8000" ]
