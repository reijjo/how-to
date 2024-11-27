# Spring Boot

## Get Started

Go to `https://start.spring.io/` to initialize your Spring Boot project

- Language -> _Java_
- Project -> _Maven_
- Spring Boot -> Choose the latest stable version (not the snapshot ones)
- Project Metadata:
- - Group -> your domain or something (not someone's else), for example _(com.reijjo)_
- - Artifact -> Project name, for example _movies_
- - Description -> _movies API_ for example
- - Packaging -> _Jar_
- - Java -> _17_
- Dependencies -> Add:
- - _Lombok_ saves you from writing of a lot of code
- - _Spring Web_
- - _Spring Data MongoDB_
- - _Spring Boot DevTools_
- Generate -> unzip the file in your projects folder and open it with IntelliJIDEA

## Database Configuration
Open file `application.properties` from _main/java/resources/_ folder
- Add to the file:
```
spring.data.mongodb.database=YOUR_DATABASE_NAME
spring.data.mongodb.uri=YOUR_DATABASE URI
```
- Let's make `.env` file for sensitive info:
- - Create `.env` file in _main/java/resources/_ folder:
```
MONGO_DB="YOUR_DATABASE_NAME"
MONGO_USER="YOUR_MONGO_USERNAME"
MONGO_PASSWORD="YOUR_MONGO_PASSWORD"
MONGO_CLUSTER="YOUR_MONGO_CLUSTER"
```
- - We need a new dependency for the `.env` file from `https://mvnrepository.com/`
- - Search _Spring Dotenv_ -> Click on the 3.0.0 version -> Copy the dependency and add it in your `pom.xml` file
- - Right-Click on your project and reload Maven
- Add `.env` to `.gitignore` file
- Now your `application.properties` file should look like this:
```
spring.data.mongodb.database=${MONGO_DB}
spring.data.mongodb.uri=mongodb+srv://${MONGO_USER}:${MONGO_PASSWORD}@${MONGO_CLUSTER}
```

## API Endpoints
- Create new Java class in your main package called _Movie_ (this is like a database table)
```java
package com.reijjo.movies;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.bson.types.ObjectId;
import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;
import org.springframework.data.mongodb.core.mapping.DocumentReference;

import java.util.List;

@Document (collection = "movies")
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Movie {

	@Id
	private ObjectId id;
	private String imdbId;
	private String title;
	private String releaseDate;
	private String trailerLink;
	private String poster;
	private List<String> genres;
	private List<String> backdrops;

	@DocumentReference
	private List<Review> reviewIds;
}

```

- Create new Java class _Review_ (this is also a database table)
```java
package com.reijjo.movies;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.bson.types.ObjectId;
import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;

@Document(collection = "reviews")
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Review {

	@Id
	private ObjectId id;
	private String body;
}

```

- Create new Java class _MovieController_ in your main package
```java
package com.reijjo.movies;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@RestController
@RequestMapping("/api/v1/movies")
public class MovieController {

	@Autowired
	private MovieService movieService;

	@GetMapping
	public ResponseEntity<List<Movie>> getAllMovies() {
		return new ResponseEntity<List<Movie>>(movieService.allMovies(), HttpStatus.OK);
	}
}
```

- Create new Java class _MovieRepository_ in your main package
```java
package com.reijjo.movies;

import org.bson.types.ObjectId;
import org.springframework.data.mongodb.repository.MongoRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface MovieRepository extends MongoRepository<Movie, ObjectId> {
}

```
- Create new Java class _MovieService_ in your main package
```java
package com.reijjo.movies;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class MovieService {

	@Autowired
	private MovieRepository movieRepository;

	public List<Movie> allMovies() {
		return movieRepository.findAll();
	}
}

```