# scalaspark

package main.scala.streamingworkouts
import org.apache.spark.SparkConf
import org.apache.spark.SparkContext
import org.apache.spark.streaming.{ Seconds, StreamingContext }
import StreamingContext._
import org.apache.kafka.clients.consumer.ConsumerRecord
import org.apache.kafka.common.serialization.StringDeserializer
import org.apache.spark.streaming.kafka010._
import org.apache.spark.streaming.kafka010.LocationStrategies.PreferConsistent
import org.apache.spark.streaming.kafka010.ConsumerStrategies.Subscribe
import org.apache.spark.storage.StorageLevel
import scala.collection.mutable.ListBuffer
import org.apache.hadoop.conf.Configuration
import org.apache.hadoop.hbase.HBaseConfiguration
import org.apache.hadoop.hbase.client.{ Put }
import org.apache.hadoop.hbase.io.ImmutableBytesWritable
import org.apache.hadoop.hbase.mapreduce.TableOutputFormat
import org.apache.hadoop.hbase.util.Bytes
import org.apache.hadoop.io.{ LongWritable, Writable, IntWritable, Text }
import org.apache.hadoop.mapred.{ TextOutputFormat, JobConf }
import org.apache.hadoop.mapreduce.lib.output.TextOutputFormat
import org.apache.spark.sql.SQLContext
import org.apache.spark.SparkConf
import org.apache.spark.SparkContext
import org.apache.spark.sql.Row
import org.apache.spark.sql.SparkSession
import org.apache.spark.SparkConf
import org.apache.spark.SparkContext
import org.apache.spark.streaming.{Seconds, StreamingContext}
import StreamingContext._
import org.apache.kafka.clients.consumer.ConsumerRecord
import org.apache.kafka.common.serialization.StringDeserializer
import org.apache.spark.streaming.kafka010._
import org.apache.spark.streaming.kafka010.LocationStrategies.PreferConsistent
import org.apache.spark.streaming.kafka010.ConsumerStrategies.Subscribe
import org.apache.spark.sql.SQLContext
import org.apache.spark.sql.SQLContext._
import org.elasticsearch.spark.streaming._
import org.apache.spark.sql.Row;
import org.elasticsearch.spark._
import java.util.Date
import org.apache.spark.sql.hive.orc._
import org.apache.spark.sql._
import java.util.Properties

object practise2 {
  
  case class schema(id:Int,name:String,tablet:String,gender:String,amt:Int)
   def main(args: Array[String])
   {
     
    val sparkConf = new SparkConf().setAppName("kafkahbase").setMaster("local[*]")
    val sparkcontext = new SparkContext(sparkConf)
    sparkcontext.setLogLevel("ERROR")
    val ssc = new StreamingContext(sparkcontext, Seconds(2))
    ssc.checkpoint("checkpointdir")
    val spark = SparkSession
      .builder()
      //.config("spark.sql.warehouse.dir", warehouseLocation)
      .enableHiveSupport()
      .getOrCreate()
      
      import spark.implicits._
       import spark.sql
       val prop = new Properties() 
       prop.put("user", "root")
       prop.put("password", "root")
       
       
       
       val kafkaParams = Map[String, Object](
      "bootstrap.servers" -> "localhost:9092",                                                                                                                      
      "key.deserializer" -> classOf[StringDeserializer],
      "value.deserializer" -> classOf[StringDeserializer],
      "group.id" -> "kafka1",
      "auto.offset.reset" -> "earliest")
      
    val topics = Array("tk30")
    val stream = KafkaUtils.createDirectStream[String, String](  
      ssc,
      PreferConsistent,                                                                                                 
         
      Subscribe[String, String](topics, kafkaParams))
      
      val kafkastream= stream.map(x=>x.value)
      kafkastream.print
      
      
      /*kafkastream.foreachRDD(x=>
        {
        if(!x.isEmpty){
          
          val data=x.map(x=>x.split(",")).map(x=>schema(x(0).toInt,x(1),x(2),x(3),x(4).toInt)).toDF()
          
          data.show()
          
          data.repartition(3).write.mode("overwrite").jdbc("jdbc:mysql://localhost/stream", "stream", prop)
         
          println("data written to sql")
          
        }
      }
        
      
      )*/
      
      
      
       
       ssc.start()
    ssc.awaitTermination()
   }
}
