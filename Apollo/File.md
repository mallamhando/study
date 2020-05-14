
* https://www.apollographql.com/docs/apollo-server/data/file-uploads/
* https://www.apollographql.com/blog/file-uploads-with-apollo-server-2-0-5db2f3f60675#File-upload-with-schema-param
* https://github.com/jaydenseric/apollo-upload-examples

> Note: When using typeDefs, Apollo Server adds `scalar Upload` to your schema, so any existing declaration of `scalar Upload` in the type definitions should be removed. If you create your schema with `makeExecutableSchema` and pass it to ApolloServer constructor using the schema param, make sure to include `scalar Upload`.

Apollo sever 에서 제공하는 `scalar upload` 를 통해 blob 데이터를 서버에 전달할수 있다.

? Blob 과 실제 데이터는 다르다. Blob 은 파일에 대한 정보를 가지고 있는 객체이고, 실제 데이터는 Blob 을 통해 읽어올수 있다. Blob 을 저장하는것이 아니라 실제 데이터를 저장해야 한다.

GraphQL 에는 실제 데이터에 대한 타입이 존재하지 않기 때문에, Blob 객체만을 전송하고 resolver 에서 Blob 객체를 통해 실제 데이터를 읽어와 저장한다.

### File Blob 을 입력받은뒤에 File 이 이동되면 Blob 읽기가 가능?
