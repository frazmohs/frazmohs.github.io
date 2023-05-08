# Lucene tutorial: Vectorization
# Introduction
Lucene is an open-source Java full-text search library that makes it easy to add search functionality to an application or website. Eventhough Lucene is often used for searching and ranking docuements, with slight modifications it can be used for feature selection and vectorization. In this tutorial I will present how to use Lucene for vectorization which is a lesser known wuality of lucene. I will start by explaning how to run search quearies as commonly stated, score them, and move on to vectorization.
# Running a search query
Here are the steps to create a Lucene search:
* Create a QueryParser: QueryParser class parses the user entered input into Lucene understandable format query.
* Create an IndexSearcher: IndexSearcher class acts as a core component which searcher indexes created during indexing process.
* Make search: Follow these steps to make search:
* Create an instance of QueryParser.
* Parse the query string using parse() method of QueryParser.
* Create an instance of IndexSearcher.
* Call the search() method of IndexSearcher by passing the parsed query and maximum number of hits as arguments.
* Get the Document: Once you get the hits from the search() method, you can get the documents by calling doc() method of IndexSearcher.
Below is the steps illustrate through code:
we will start with having a function to add documents to the index:
```
private static void addDoc(IndexWriter w, String title, String isbn) throws IOException {
  Document doc = new Document();
  doc.add(new TextField("title", title, Field.Store.YES));
  doc.add(new StringField("isbn", isbn, Field.Store.YES));
  w.addDocument(doc);
}
```
We will creat out index and scorer
```
StandardAnalyzer analyzer = new StandardAnalyzer();
Directory index = new ByteBuffersDirectory();

IndexWriterConfig config = new IndexWriterConfig(analyzer);

IndexWriter w = new IndexWriter(index, config);
addDoc(w, "Lucene in Action", "193398817");
addDoc(w, "Lucene for Dummies", "55320055Z");
addDoc(w, "Managing Gigabytes", "55063554A");
addDoc(w, "The Art of Computer Science", "9900333X");
w.close();
```
We will create our query, run searches and display the top features (in this case 10)
```
String querystr = args.length > 0 ? args[0] : "lucene";
Query q = new QueryParser("title", analyzer).parse(querystr);
int hitsPerPage = 10;
IndexReader reader = DirectoryReader.open(index);
IndexSearcher searcher = new IndexSearcher(reader);
TopDocs docs = searcher.search(q, hitsPerPage);
ScoreDoc[] hits = docs.scoreDocs;
```
# Vectorization



