# Lucene tutorial: Vectorization
By Sayyed Faraz Mohseni

# Introduction
Lucene is an open-source Java full-text search library that makes it easy to add search functionality to an application or website. Even though Lucene is often used for searching and ranking docuements, with slight modifications it can be used for feature selection and vectorization. In this tutorial I will present how to use Lucene for vectorization which is a lesser known quality of lucene. I will start by explaning how to run search queries as commonly stated, score them, and move on to vectorization wich is a lesser discussed topic.
# Running a search query
Below are the steps to do a Lucene search:
* Create a QueryParser: QueryParser class parses the user entered input into Lucene understandable format query.
* Create an IndexSearcher: IndexSearcher class acts as a core component which searcher indexes created during indexing process.
* Create an instance of QueryParser.
* Parse the query string using parse() method of QueryParser.
* Create an instance of IndexSearcher.
* Call the search() method of IndexSearcher by passing the parsed query and maximum number of hits as arguments.
* Get the Document: Once you get the hits from the search() method, you can get the documents by calling doc() method of IndexSearcher.
Below is the steps illustrate through code and an example:

we will start with having a function to add documents to the index.
```
private static void addDoc(IndexWriter w, String title, String isbn) throws IOException {
  Document doc = new Document();
  doc.add(new TextField("title", title, Field.Store.YES));
  doc.add(new StringField("isbn", isbn, Field.Store.YES));
  w.addDocument(doc);
}
```

We will creat our index and scorer

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
Asuume that your goal is to vectorize the sentences "I am a cat" and "I am a dog". I show how to vectorize this sentence using the standard scoring method available in Lucene (Okapi BM25). the scoring method can be changed in Lucene to any socring we want. The steps to vectorize are as follows:

* Construct a list of all available words in all docuemtns. In our case there are 5 unique words and out list would be ["I", "am". "a", "cat", "dog"].
* Now as explained above, run each of these queries in our list among the two docuements (sentences) that we have.
* Lucene will give us the scores for each of these queries. We will use them to vectorize our sentences.
An example of the two vecotized sentences is follows if we consider our vector to be ["I", "am". "a", "cat", "dog"]. (The numbers are exmamples)
* "I am a cat" 
* [ 0.23, 0.13, 0.12, 0.11, 0]
* "I am a dog" 
* [ 0.23, 0.13, 0.12, 0, 0.11]

# Additional notes
Vectorizing using Lucene has many advanatages compared to available libraries, as we can use any search engine score function for our vectorization instead of the often available tf-idf.

