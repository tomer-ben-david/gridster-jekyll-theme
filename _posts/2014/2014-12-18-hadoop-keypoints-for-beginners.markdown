---
layout: post
title:  "Hadoop keypoints for beginners"
date:   2014-12-18 10:18:00
categories: dev,datascience
comments: true
published: false
---

#### Key Concepts

* Every mapper communicates with all reducers (potentially sending data to all of them).
* `Shuffle` - communication from mappers to reducers.
* `Block Size` - Files are split to blocks you can any file size it would just get split into blocks (64MB / 128MB ...)
* `Partitioner` - Splits map result to reducer the by `hash` so same keys always reach same reducers.  **same key will always reach same reducer**.
* If you want your result reside in single file (cant be multiple blocks) you need a **single reducer**.

#### Hadoop default configuration

* `/etc/hadoop/conf`

#### Jobs management

1. Master sends the actual `jar` to be executed to data nodes.
1. Hadoop sends data from mappers to reducers even before `mappers` finished in order to have `reducers` be able to already start working.
1. `hadoop jar` is the command that will run your jar (you should have already uploaded it to hadoop cluster with `-put`
1. Show list of running jobs `mapred job -list`

#### Hadoop Map Reduce

1. `FileInputFormat` takes a file and if we have multiple blocks splits it to multiple mappers.
1. You should have a main (not mapper and not reducer which is your `main`) it receives the command line parameters uses the `FileInputFormat` to split the file (which has usually multiple `blocks` to multiple `mappers`.  The `FileInputFormat` uses the `InputPath`. We have similarly `FileOutputFormat`.
1. When referring to `Strings` for example in output you refer to `job.setOutputKeyClass(Text.class)`
1. You can give the `inputPath` multiple `*` for all files in directory or all directories or just use the `Jobs` api to add multiple paths.
1. You have access to main `Job` object which handles the job `from` your `main`.
1. The `id` of the `key` object brought to `mapper` is the offset from file (it has also the actual `key`).
1. In mapper you use `context.write` in order to write the result (`context` is parameter to `mapper`).
1. If you write a string as output from mapper you write it with `new Text(somestr)`
1. `mapper` is a pure function it gets input `key, value` and emits `key, value` or multiple keys and values.  So as its as much pure as possible its not intended to perform states meaning it will not combine results for mapping internally (see `combiners` if you need that).
1. `Reducers` receive a `key -> values` it will get called multiple times with **sorted** `keys`.

#### Example Hadoop Map Reduce

You need to have 3 files.

1. Job manager.
2. Mapper.
3. Reducer.

Let's see each of them in an exmaple.

*1. JobManager*

{% highlight java %}
  /**
   * Setup the job.
   */
  public static void main(String[] args) throws Exception {

    // inputs
    if (args.length != 2) {
      System.out.printf("Usage: YourJob <input dir> <output dir>\n");
      System.exit(-1);
    }

    // Set job inputs outputs.
    Job job = new Job();
    job.setJarByClass(YourJob.class);
    job.setJobName("Your Job Length");
    FileInputFormat.setInputPaths(job, new Path(args[0]));
    FileOutputFormat.setOutputPath(job, new Path(args[1]));
    job.setMapperClass(YourMapper.class);
    job.setReducerClass(YourReducer.class);
    job.setMapOutputKeyClass(Text.class);
    job.setMapOutputValueClass(IntWritable.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(DoubleWritable.class);

    // exit..
    boolean success = job.waitForCompletion(true);
    System.exit(success ? 0 : 1);
  }
{% endhighlight %}

*2. YourMapper*

{% highlight java %}
  public class YourMapper extends Mapper<LongWritable, Text, Text, IntWritable> { // <InputKey, InputValue, OutputKey, OutputValue>

    @Override
    public void map(LongWritable key, Text value, Context context)
        throws IOException, InterruptedException {

  	  final String line = value.toString();
  	  for (String word: line.split("\\W+")) {
         context.write(context, new Text(word), new IntWritable(1)); // write mapper output.
  	  }
    }
{% endhighlight %}

*3. YourReducer*

{% highlight java %}
public class YourReducer extends Reducer<Text, IntWritable, Text, IntWritable> {

  /**
   * Note you get a key and then a list of all values which were emitted for this key.
   * The keys which are handed to reducers are sorted.
   */
  @Override
  public void reduce(Text key, Iterable<IntWritable> values, Context context)
      throws IOException, InterruptedException {

      int count = 0;
	  for (IntWritable value: values) {
		  count++;
	  }
      context.write(new Text(key), new IntWritable(count)); // we use context to write output to file.

  }

{% endhighlight %}

#### Tool Runner

Formalizes the command line arguments into hadoop jobs so you don't need to mess with them yourself they will have the same pattern.
`hadoop jar somejar.jar YourMain -D mapred.reduce.tasks=2 someinputdir someoutputdir`

#### State VS Stateless

map reduce are inherently stateless (pure functions), very nice, you should stick to it as much as possible. however if you need state persisted (but reallymwar, try to find other ways before reverting to state) you will use the `public void setup(Context context)` method its a standard setup method.  Likewise you have a `cleanup` method which is called after map/reduce finishes.  It's quiete common that in `cleanup` if you used some states you will write out the state to disk.
*Note* In order to use `toolrunner` your job main class should `extends Configured implements Tool`  Then instead of `main` method you will `override` the `run` method inherited from `toolrunner`.

{% highlight java %}
public class YourJob extends Configured implements Tool {

	@Override
	public int run(String[] args) throws Exception {
		// TODO Auto-generated method stub
		return 0;
	}
	.
	.
}
{% endhighlight %}
You still need the `main` method as you need to explicitly run the `run` method.

```java
public static void main(String[] args) throws Exception {
  int exitCode = ToolRunner.run(new Configuration(), new WordCount(), args);
  System.exit(exitCode);
}
```

#### Combiner the local mapper mini-reducer

In wordcount mapper resulted with `1` for every word.  Which is kind of silly.  In order to reduce the noise and communication between mappers and reducers you can use a combiner to combine the mapper results.

`job.setCombinerClass(MiniReducer.class)` it can actually be your standard `reducer`.

mapper: `map(pointer,house) --> map(house,1)`
mapper: `map(pointer,house) --> map(house,1)`
combiner: `map(house,1) --> map(house,2)` Note: Combiner runs locally in the mapper.
reducer: `map(house,1) --> map(house,5)`

#### No map reduce, simple HDFS access

You can also access the `HDFS` programatically without any relation to map/reduce.

{% highlight java %}
Filesystem.get(conf);
Path p = new Path("/somepath/file");
{% endhighlight %}

#### Distributed Cache

A command to distribute files to all mappers so that when they start up they will already have the data locally (usually for configuration or other side data).
`DistributedCache.addCacheFile..` and more commands see its API for more info.
you can skip using this api and just pass `-files conf1.conf conf2.conf ...` to `toolrunner` and it will distribute te in the `hadoop jar` task.
Note that when mapper loads this file it would not need any directories it simply loads the filename no directory needed (mapper will have it in current directory).

#### Counters

Maps, Reducers can read and write `(group, name) --> int-value` should be used only for management not business logic (each of them can read any of the other). (not atomic).

`context.getCounter("counter-group","counter-name").increment(1);`

#### Useful hadoop commands

* `hadoop fs -rm -r myoutputdir`
* `hadoop fs -cat myoutputdir/part-r-00000 | head`
* `hadoop jar myjob.jar YourJobMain -D someparam=true inputdir outputdir`
* `hadoop jar myjob.jar MyJobMain -fs=file:/// -jt=local inputdir outputdir` (run local job)

#### Partitioners
* `TotalOrderPartitioner` so that you can partition the data and still preserve some order in the different files (like a-h file1 h-t partition 2 and still preserve that the files are in similar size not most of the data in one file (for example if you have many words starting with m and only one starting with b)).

#### CAUSION

* When you receive parameter to your reduce job `Text value` multiple times (obviously) then it might be the same reference but the value can be different (it will reuse the param reference to mapper but will change hte value so **do not** store the local `value` it might mean different in different runs.

#### Scripting languages

*Hive*

1. Kind of an `SQL` for map reduce jobs.
1. Can add user defined functions.

*Pig*

1. Kind of a `scripting language` for map reduce jobs.

#### Other Components

1. Data transfers - `Flume`, `Scoop`
1. Workflow management - `oozie`, `YARN`

*Impala*

1. Instead of start, read data, write data, those are living servers on hadoop cluster, so much faster (not standard `mapreduce`, not even `mapreduce`)
1. Query language similar to `HiveQL`

