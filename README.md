# Epróba - testowanie lokalnie

1. Pobierz i zainstaluj [Docker Desktop](https://www.docker.com/products/docker-desktop/)

2. Po restarcie uruchom Docker Desktop
![Screenshot 2024-09-25 111836](https://github.com/user-attachments/assets/bc6d1589-98a5-43d7-918e-854b44d1546c)

3. Wyszukaj "eproba" i kliknij `Run` przy `antonicz/eproba`
![Screenshot 2024-09-25 111849](https://github.com/user-attachments/assets/74cfbb7b-d9aa-441a-9430-1081984ff406)

4. Po pobraniu wyskoczy okienko, kliknij na nim `Optional settings`
![Screenshot 2024-09-25 112135](https://github.com/user-attachments/assets/36344223-a6f2-4759-a5db-c58cdb1637bb)

5. Wpisz `8000` w pierwszym polu w `Ports` i naciśnij `Run`
![Screenshot 2024-09-25 112158](https://github.com/user-attachments/assets/4a8ae22a-f4c7-4821-a3f4-de82307cc8a8)

6. Kliknij w zakładkę `Exec`
![Screenshot 2024-09-25 112233](https://github.com/user-attachments/assets/21d761c6-b4a0-4e71-b30a-98ce9d9c8f42)

7. Pojawi się konsola jak na zdjęciu poniżej, kliknij na nią żeby kursor zaczął migać
![Screenshot 2024-09-25 112550](https://github.com/user-attachments/assets/f32f8f20-5d6c-4392-90a4-99fc7bff4222)

8. Skopiuj i wklej poniższą komendę i naciśnij `Enter`. Aby wkleić użyj `Ctrl + Shift + V`
```
python manage.py shell --command="
from apps.users.models import User; 
from apps.teams.models import Team, District, Patrol; 
from django.core.management import call_command; 
import random;

# Load initial district data
call_command('loaddata', 'districts_default.json')

# Create teams and patrols for each district
[district.team_set.create(name=f'Drużyna 1 {district.name}', short_name=f'd1{district.name}').patrol_set.create(name='Zastęp 1') for district in District.objects.all()]

# Fetch all patrols
patrols = list(Patrol.objects.all())

# Function to assign a random patrol
def random_patrol():
    return random.choice(patrols) if patrols else None

# Create a superuser
superuser = User.objects.create(
    email='superuser@example.com', 
    email_verified=True,
    patrol=random_patrol(),
    is_superuser=True,
    is_staff=True
)
superuser.set_password('superpassword123')

# Create a team leader
team_leader = User.objects.create(
    email='teamleader@example.com', 
    email_verified=True,
    patrol=random_patrol(), 
    function=4
)
team_leader.set_password('leaderpassword123')

# Create a normal user
normal_user = User.objects.create(
    email='normaluser@example.com', 
    email_verified=True,
    patrol=random_patrol()
)
normal_user.set_password('userpassword123')

# Save the users
superuser.save()
team_leader.save()
normal_user.save()

print('Dane początkowe zostały zaimportowane, możesz się teraz zalogować')
"
```
![Screenshot 2024-09-25 124419](https://github.com/user-attachments/assets/11c863ca-dacb-46dd-943d-8b8a9b7d231f)

9. Gotowe! Możesz teraz używać Epróby wchodząc na http://localhost:8000, API można testować wchodząc na http://localhost:8000/api/schema/swagger-ui/ lub http://localhost:8000/api/

Dane logowania:  
Admin: `superuser@example.com` / `superpassword123`  
Drużynowy: `teamleader@example.com` /  `leaderpassword123`  
Zwykły użyrkownik: `normaluser@example.com` / `userpassword123`  
