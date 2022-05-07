# Connection PostgreSQL, NodeJS and Express Server | Practice Launch X

### Necessary technologies
- Node.js
- Prisma
- Express
- PostgreSQL

### Steps

1. Create a JS project with npm init
2. Install Prisma package Prisma (`npm install prisma --save-dev`)

Prisma is an open source next-generation ORM (Object-Relational Mapping) that facilitates the access to the database with an automated generated and type-safe query builder that adapts to our database schema.

3. Install Express server (`npm install express --save-dev`)
4. Execute prisma (npx prisma initi), this will create an initial schema.prisma
5. Create explorers_api database in PostgreSQL
6. Modify the .env file and add your credentials
`DATABASE_URL="postgresql://TUUSUARIO:PASSWORD@localhost:5432/explorers_api?schema=public"`

7. Add the next model

```mermaid
classDiagram
class Explorer {
  int id autoincrement
	string name
	varchar username
	varchar mission
	boolean azureCertification default false
	datetime dateCreated
	datetime lastUpdated 
}
```

To achieve this, add the next code in shcema.prisma:

```javascript
model Explorer {
  id Int @id @default(autoincrement())
  name String @unique
  username String @db.VarChar(255)
  mission String @db.VarChar(255)
  azureCertification Boolean @default(false)
  dateCreated DateTime @default(now())
  lastUpdated DateTime @updatedAt
}
```

8. Create migration for Explorer model (`npx prisma migrate dev --name init`)
When you run this command, it will generate the table on the database.

9. Create a file prisma/seed.js

```javascript
const { PrismaClient } = require('@prisma/client');
const prisma = new PrismaClient();

(async function main() {
  try {
    const woopa = await prisma.explorer.upsert({
      where: { name: 'Woopa' },
      update: {},
      create: {
        name: 'Woopa',
				username: 'ajolonauta',
				mission: 'Node'
      },
    });

    const woopa1 = await prisma.explorer.upsert({
      where: { name: 'Woopa1' },
      update: {},
      create: {
        name: 'Woopa1',
				username: 'ajolonauta1',
				mission: 'Node'
      },
    });

    const woopa2 = await prisma.explorer.upsert({
      where: { name: 'Woopa 2' },
      update: {},
      create: {
        name: 'Woopa 2',
				username: 'ajolonauta2',
				mission: 'Java'
      },
    });

    const woopa3 = await prisma.explorer.upsert({
      where: { name: 'Woopa 3' },
      update: {},
      create: {
        name: 'Woopa 3',
				username: 'ajolonauta3',
				mission: 'Node'
      },
    });

    const woopa4 = await prisma.explorer.upsert({
      where: { name: 'Woopa 4' },
      update: {},
      create: {
        name: 'Woopa 4',
				username: 'ajolonauta4',
				mission: 'Node'
      },
    });

    const woopa5 = await prisma.explorer.upsert({
      where: { name: 'Woopa 5' },
      update: {},
      create: {
        name: 'Woopa 5',
				username: 'ajolonauta5',
				mission: 'Node'
      },
    });

    console.log('Create 6 explorers');
  } catch(e) {
    console.error(e);
    process.exit(1);
  } finally {
    await prisma.$disconnect();
  }
})();
```

10. Run the previous file `node prisma/seed.js`, this will add the data in your database
11. Create the file `server.js` and add the CRUD endpoints (Create, Read, Update and Delete)

```javascript
const express = require('express');
const app = express();
app.use(express.json());
const port = process.env.PORT || 3000;

// Require para usar Prisma
const { PrismaClient } = require('@prisma/client');
const prisma = new PrismaClient();

app.get('/', (req, res) => {
  res.json({message: 'alive'});
});

app.listen(port, () => {
  console.log(`Listening to requests on port ${port}`);
});

app.get('/explorers', async (req, res) => {
  const allExplorers =  await prisma.explorer.findMany({});
  res.json(allExplorers);
});

app.get('/explorers/:id', async (req, res) => {
  const id = req.params.id;
  const explorer = await prisma.explorer.findUnique({where: {id: parseInt(id)}});
  res.json(explorer);
});

app.post('/explorers', async (req, res) => {
  const explorer = {
    name: req.body.name,
    username: req.body.username,
    mission: req.body.mission
  };
  const message = 'Explorer creado.';
  await prisma.explorer.create({data: explorer});
  return res.json({message});
});

app.put('/explorers/:id', async (req, res) => {
  const id = parseInt(req.params.id);

  await prisma.explorer.update({
    where: {
      id: id
    },
    data: {
      mission: req.body.mission
    }
  })

  return res.json({message: "Actualizado correctamente"});
});

app.delete('/explorers/:id', async (req, res) => {
  const id = parseInt(req.params.id);
  await prisma.explorer.delete({where: {id: id}});
  return res.json({message: "Eliminado correctamente"});
});
```

12. Run the server `node server.js`

13. Open Postman and add the requests for each endpoint

### Create a new explorer:
Type: POST 

URL request: localhost:3000/explorers

Body (type json): 
```javascript
{
    "name":"Explorer10", 
    "username":"explorer10", 
    "mission": "Node"
}
```
![image](https://user-images.githubusercontent.com/48570016/167271396-d083b482-61e9-4b15-aef6-148ee56fa375.png)

Response:
```javascript
{
    "message": "Explorer creado."
}
```

![image](https://user-images.githubusercontent.com/48570016/167271379-dbe295d6-a0ae-4746-a112-b68eebb832a0.png)


### Update explorer:
Type: PUT 

URL request: localhost:3000/explorers/5

Body (type json):
```javascript
{"mission":"Node"}
```

Response: 
```javascript
{
    "message": "Actualizado correctamente"
}
```
![image](https://user-images.githubusercontent.com/48570016/167271426-063f3d83-c99f-477d-90f4-78310fad2ee3.png)

You can validate the changes on your database

### Read all explorers:
Type: GET 

URL request: localhost:3000/explorers

Response: When you send the request you will get a json with all the explorers as a response

![image](https://user-images.githubusercontent.com/48570016/167271438-d86954a5-17a1-4df4-a8b9-4812eb88032f.png)

### Read explorer by ID:
Type: GET 

URL request: localhost:3000/explorers/5

Response: When you send the request you will get a json with the information of the explorer with the given ID

![image](https://user-images.githubusercontent.com/48570016/167271448-40b39883-8377-4052-806a-b1dd5fddc354.png)


### Delete explorer:
Type: DELETE 

URL request: localhost:3000/explorers/5

```javascript

Response: 
{
    "message": "Eliminado correctamente"
}
```
![image](https://user-images.githubusercontent.com/48570016/167271456-26bd8f09-38e1-451a-9ed6-259742527ed5.png)

### Keep practicing

14. Add the next model

```mermaid
classDiagram
class Student {
  int id autoincrement
	string name
	varchar lang
	varchar missionCommander
	int enrollments
	boolean hasCertification default false
	datetime dateCreated
	datetime lastUpdated 
}

```

To achieve this, add the next code in shcema.prisma:

```javascript
model Student {
  id Int @id @default(autoincrement())
  name String @unique
  lang String @db.VarChar(255)
  missionCommander String @db.VarChar(255)
  enrollments Int
  hasCertification Boolean @default(false)
  dateCreated DateTime @default(now())
  lastUpdated DateTime @updatedAt
}
```

15. Create migration for Student model (`npx prisma migrate dev --name init`)
When you run this command, it will generate the table `student` on the database.

16. Add new registers on your file prisma/seed.js and run it

```javascript
const julieta = await prisma.student.upsert({
      where: { name: 'Julieta' },
      update: {},
      create: {
        name: 'Julieta',
        lang: 'Node',
        missionCommander: 'Carlo Gilmar',
        enrollments: 1
      },
    });

    const julieta1 = await prisma.student.upsert({
      where: { name: 'Julieta1' },
      update: {},
      create: {
        name: 'Julieta1',
        lang: 'Java',
        missionCommander: 'Fernanda Ochoa',
        enrollments: 2,
        hasCertification: true
      },
    });

    const julieta2 = await prisma.student.upsert({
      where: { name: 'Julieta2' },
      update: {},
      create: {
        name: 'Julieta2',
        lang: 'PHP',
        missionCommander: 'Sarthak Shrivastava',
        enrollments: 3,
        hasCertification: true
      },
    });

    console.log('Create 3 students');
```

17. Add the CRUD endpoints (Create, Read, Update and Delete) for model Student in server.js file.
```javascript
app.get('/students', async (req, res) => {
  const allStudents =  await prisma.student.findMany({});
  res.json(allStudents);
});

app.get('/students/:id', async (req, res) => {
  const id = req.params.id;
  const student = await prisma.student.findUnique({where: {id: parseInt(id)}});
  res.json(student);
});

app.post('/students', async (req, res) => {
  const student = {
    name: req.body.name,
    lang: req.body.lang,
    missionCommander: req.body.missionCommander,
    enrollments: req.body.enrollments,
    hasCertification: req.body.hasCertification
  };
  const message = 'student creado.';
  await prisma.student.create({data: student});
  return res.json({message});
});

app.put('/students/:id', async (req, res) => {
  const id = parseInt(req.params.id);

  await prisma.student.update({
    where: {
      id: id
    },
    data: {
      name: req.body.name,
      lang: req.body.lang,
      missionCommander: req.body.missionCommander,
      enrollments: req.body.enrollments,
      hasCertification: req.body.hasCertification
    }
  })

  return res.json({message: "Student - Actualizado correctamente"});
});

app.delete('/students/:id', async (req, res) => {
  const id = parseInt(req.params.id);
  await prisma.student.delete({where: {id: id}});
  return res.json({message: "Student - Eliminado correctamente"});
});
```

13. Open Postman and add the requests for each endpoint

### Create a new student:
Type: POST 

URL request: localhost:3000/students

Body (type json): 
```javascript
{
    "name":"Julieta4",
    "lang":"Javascript",
    "missionCommander":"Rodrigo Martínez",
    "enrollments": 4,
    "hasCertification": false
}
```

Response:
```javascript
{
    "message": "student creado."
}
```
![image](https://user-images.githubusercontent.com/48570016/167271470-05cae022-bcf0-47e6-8e59-2d9c93e9f74a.png)

### Update student:
Type: PUT 

URL request: localhost:3000/students/6

Body (type json):
```javascript
{
    "name":"Julieta Delgado",
    "lang":"NodeJS",
    "missionCommander":"Carlo Gilmar",
    "enrollments": 4,
    "hasCertification": true
}
```

Response: 
```javascript
{
    "message": "Student - Actualizado correctamente"
}
```
![image](https://user-images.githubusercontent.com/48570016/167271479-c7ed9c6f-8580-465d-82d5-6d1938b8c5df.png)

You can validate the changes on your database

### Read all students:
Type: GET 

URL request: localhost:3000/students

Response: When you send the request you will get a json with all the students as a response

![image](https://user-images.githubusercontent.com/48570016/167271503-e7ee48ae-623b-4db2-8d2e-4c17852f227b.png)

### Read student by ID:
Type: GET 

URL request: localhost:3000/students/5

Response: When you send the request you will get a json with the information of the student with the given ID

![image](https://user-images.githubusercontent.com/48570016/167271516-a8d16b33-4d1b-4bff-b49f-5e39a6ace0c4.png)

### Delete student:
Type: DELETE 

URL request: localhost:3000/students/5
```javascript
Response: 
{
    "message": "Student - Eliminado correctamente"
}
```
![image](https://user-images.githubusercontent.com/48570016/167271523-234991ee-ecb6-46f8-a29f-7c18d6a62abf.png)
