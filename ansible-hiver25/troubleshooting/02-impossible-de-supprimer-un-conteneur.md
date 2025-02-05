# Erreur :

- J'ai essayé docker stop 66 / docker kill 66 / docker rm -f 66 / sudo kill -9 / sudo kill -15 / sudo kill "$(jobs -p %1)"
 ==> toujours le daemon est en cours d'exécution !
- ==> Error response from daemon: cannot stop container #254

# Résolution :

- sudo systemctl restart docker.socket docker.service
- sudo docker image rm -f $(sudo docker image ls -q)

# Référence: 
- https://stackoverflow.com/questions/71477749/error-response-from-daemon-cannot-kill-container-permission-denied-how-to-kil

