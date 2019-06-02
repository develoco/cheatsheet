https://medium.com/codingthesmartway-com-blog/creating-a-graphql-server-with-node-js-and-express-f6dddc5320e1

# Server script

Get the dependencies:

    npm i graphql express express-graphql

The server can be started with `node server.js`
 
    let express = require('express') 
    let express_graphql = require('express-graphql')
    let { buildSchema } = require('graphql')

    // GraphQL schema
    let schema = buildSchema(`type Query { message: String }`)

    // Root resolver
    let rootValue = { message: () => 'Hello World!' }

    // Create an express server and a GraphQL endpoint
    let app = express()
    app.use('/graphql', express_graphql({ schema, rootValue, graphiql: true }))
    app.listen(4000, () => console.log('Express GraphQL Server Now Running On localhost:4000/graphql'))

# Queries

Define the schema

    let schema = buildSchema(`
        type Query {
            course(id: Int!): Course
            courses(topic: String): [Course]
        },
        type Course {
            id: Int
            title: String
            author: String
            description: String
            topic: String
            url: String
        }
    `)

Populate with data

    let coursesData = [
        {
            id: 1,
            title: 'The Complete Node.js Developer Course',
            author: 'Andrew Mead, Rob Percival',
            description: 'Learn Node.js by building real-world applications with Node, Express, MongoDB, Mocha, and more!',
            topic: 'Node.js',
            url: 'https://codingthesmartway.com/courses/nodejs/'
        },
        ...
    ]

Define queries

    let rootValue = {
      course: args => {
        let { id } = args
        return coursesData.filter(course => course.id == id)[0]
      },
      courses: args => {
        let {topic} = args
        if (topic) {
          return coursesData.filter(course => course.topic === topic)
        } else {
          return coursesData
        }
      }
    }

# Client side

Compose in GraphiQL

    query getSingleCourse($courseID: Int!) {
        course(id: $courseID) {
            title
            author
            description
            topic
            url
        }
    }

Query parameters

    { 
        "courseID":1
    }
    
Aliases and fragments. Alias is the  `course1:` bit

    query getCourseWithFragments($courseID1: Int!, $courseID2: Int!) {
          course1: course(id: $courseID1) {
                 ...courseFields
          },
          course2: course(id: $courseID2) {
                ...courseFields
          } 
    }

Fragments

    fragment courseFields on Course {
      title
      author
      description
      topic
      url
    }

# Mutations

Define in schema

    let schema = buildSchema(`
      ...
      type Mutation {
        updateCourseTopic(id: Int!, topic: String!): Course
      }
      ...
    `)

Implement

    let rootValue = {
      ...
      updateCourseTopic: ({id, topic}) => {
        coursesData.forEach(course => {
          if (course.id === id) {
            course.topic = topic
          }
        })
        return coursesData.filter(course => course.id === id) [0]
      }
    }

On client, target the mutation

    mutation updateCourseTopic($id: Int!, $topic: String!) {
      updateCourseTopic(id: $id, topic: $topic) {
        ... courseFields
      }
    }

Run with parameters

    {
      "id": 3,
      "topic": "New topico"
    }
