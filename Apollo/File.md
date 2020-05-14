
* https://www.apollographql.com/docs/apollo-server/data/file-uploads/
* https://www.apollographql.com/blog/file-uploads-with-apollo-server-2-0-5db2f3f60675#File-upload-with-schema-param
* https://github.com/jaydenseric/apollo-upload-examples

> Note: When using typeDefs, Apollo Server adds `scalar Upload` to your schema, so any existing declaration of `scalar Upload` in the type definitions should be removed. If you create your schema with `makeExecutableSchema` and pass it to ApolloServer constructor using the schema param, make sure to include `scalar Upload`.

Apollo sever 에서 제공하는 `scalar upload` 를 통해 blob 데이터를 서버에 전달할수 있다.

Blob 과 실제 데이터는 다르다. Blob 은 파일에 대한 정보를 가지고 있는 객체이고, 실제 데이터는 Blob 을 통해 읽어올수 있다.
