# BDCoE-Trainee-2017


WordCount Program (With Explanation):


/*
Include these java libraries
required by mapper and
reducer classes, also Required
to compile and run java
classes

Class Name should be same with Java
Programe name
*/

package Wordcount;
import java.io.IOException;
import java.util.*;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.conf.*;
import org.apache.hadoop.io.*;
import org.apache.hadoop.mapreduce.*;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.input.TextInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.mapreduce.lib.output.TextOutputFormat;

public class Wordcount
{

/*
The Mapper Class defines the input key
and value data types and the output key
and value data types
*/

public static class hmap extends Mapper<LongWritable, Text, Text, IntWritable>
{

/*
Declare a Intwritable class variable “one” and initialiaze
with 1.it a type of output value

Declare a Text class object “word” , Word object store the single string (word)
. It is a type of output key output key
*/

	private final static IntWritable one = new IntWritable(1);

	private Text word = new Text();

/*
Map function takes one document’s text and
emits key-value pairs for each word found in
the document
*/

	public void map(LongWritable key, Text value, Context context)throws IOException,InterruptedException
{


String line = value.toString();

/*
tokennizer divides a sentence into individual words
*/

StringTokenizer tokenizer = new StringTokenizer(line);


/*
The hasMoreTokens() method returns true while there are more tokens to be extracted
*/


		while (tokenizer.hasMoreTokens())
		{

/*
The nextToken() method is used to extract consecutive tokens and set
word as each input keyword
*/

			word.set(tokenizer.nextToken());

/*
Context is something which lets you pass key-value pairs forward . It
create pair of <word,1> ie, key and value
*/	
	
			context.write(word, one);

		}
	}

}
public static class hreduce extends Reducer<Text, IntWritable, Text, IntWritable>
{

	public void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException,InterruptedException

/*
The first two refers to data type of input key and value to the reducer and
last two refers to data type of output key and value
*/
	{

		int sum = 0;

/*
Iterates through all the values available
with a key and add them together and
give the final result as the key and sum of
its value
*/

		for(IntWritable val: values)

		{

			sum += val.get();
	

		}



		context.write(key, new IntWritable(sum));
	
	}

}

public static void main(String[] args) throws Exception
{

	Configuration conf = new Configuration();

/*
Create the Configuration object
*/

	Job job = new Job(conf,"Wordcount");

/*
Create a Job name with word count
*/

	job.setJarByClass(Wordcount.class);

	job.setOutputKeyClass(Text.class);

	job.setOutputValueClass(IntWritable.class);

/*
Setting configuration object with the data type of output key & value
*/

	job.setMapperClass(hmap.class);

/*
Provide the mapper class & Reducer class
*/
	job.setReducerClass(hreduce.class);

	job.setInputFormatClass(TextInputFormat.class);

/*
Set the Format of Input & Output Class
*/

	job.setOutputFormatClass(TextOutputFormat.class);
	FileInputFormat.addInputPath(job, new Path(args[0]));
	FileOutputFormat.setOutputPath(job, new Path(args[1]));

/*
Set the location From where the mapper will read input & Reducer will
write output
*/

	job.waitForCompletion(true);

	}
}
