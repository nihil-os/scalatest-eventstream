scalatest stream-specs
----------------------

Kinesis usage
-------------
- seup auth profile in `application.properties`

```
class KinesisEmbeddedStreamSpecs extends FunSuite with BeforeAndAfterEach {

  val eventStream = new KinesisEmbeddedStream

  implicit val streamConfig = StreamConfig(stream = "TestStream", numOfPartition = 1)

  var partitionId = ""

  override protected def beforeEach(): Unit = {
    partitionId = eventStream.startBroker._2.head
  }
  override protected def afterEach(): Unit = eventStream.destroyBroker

  test("appends and consumes an event") {

    implicit val consumerConfig = ConsumerConfig(name = "TestStreamConsumer", partitionId = partitionId, strategy = "TRIM_HORIZON")
    eventStream.appendEvent("TestStream", """{"eventId" : "uniqueId", "data" : "something-secret"}""".stripMargin)

    Thread.sleep(1500)

    assert(eventStream.consumeEvent(streamConfig, consumerConfig, streamConfig.stream).size == 1)
  }

}

```
