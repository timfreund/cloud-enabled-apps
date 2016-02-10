Files and directories are the second form of input and output many of
us learned about, right after we wrote a program that accepted our
name in the terminal and gave us a personalized greeting in response.
Because files and directories are still a fundamental unit of software
development, we build systems to enable us to use files in the same
old ways despite everything else changing around our software.

NFS, CIFS, and clustered file systems allow us to scale the files and
directories mindset across multiple servers.  This works well as long
as every machine has a fast and reliable network link to the file
server, and each machine in a cluster carefully avoids overwriting
changes made by the other machines.  In other words, this works well
for small proofs of concept run over short periods of time in highly
controlled environments.

If we want to build applications that can span the globe, we need to
stop thinking about network file systems as valid configuration and
data containers.  It's an inefficient abstraction in 2016.

File systems and abstractions.  If you're old enough to smile fondly
when someone mentions Joel Spolsky, you're likely thinking about his
2002 article called [The Law of Leaky Abstractions](http://www.joelonsoftware.com/articles/LeakyAbstractions.html)

Of course, it's equally likely that you've never heard of Joel, and
some readers may be even younger than the article in question.  If you
like clear but somewhat long winded explanations of software and
business concepts, you should check out Joel's work.  This is text, so
it'll be here when you get back.

> What is a file system? It's a way to pretend that a hard drive isn't
> really a bunch of spinning magnetic platters that can store bits at
> certain locations, but rather a hierarchical system of
> folders-within-folders containing individual files that in turn
> consist of one or more strings of bytes.

Some abstractions are better than others.  Filesystems on a hard drive
are a fairly reliable and incredibly useful abstraction over code that
manually moves disk heads across a platter of iron oxide.

Network filesystems let us pretend like a remote filesystem is local,
and everything works out fine until it goes horribly wrong.

NFS sucks, and CIFS sucks, but they're both at the heart of many
application designed to enable horizontal scaling and ensure identical
configurations across many machines.  How do we extract ourselves
from this problem?

I know I was just trash talking two well known abstraction layers for
sharing files, but don't close this tab while I introduce you to a new
abstraction layer for sharing files.

I'm pretty sure I just heard your eyes roll despite you reading this sometime
in the future.  Give me just one short paragraph, OK?

Object storage systems expose a HTTP endpoint for savings, updating,
retrieving, and deleting files or other blobs of data.  Behind the API
endpoint the data is automatically duplicated on multiple disks on
multiple servers and often in multiple locations.  Because they use
HTTP, clients are available in any language: Java, C#, Python, Ruby,
and even Erlang.  This includes ready built clients like curl, wget, and
your browser, too.

TKTK Insert exercises to add, read, and delete
on S3, Swift/Ceph, Azure
with Java, C#, Python, PHP, curl
(libcloud and similar will make this a shorter task than it looks)

Before you annoy every other developer on your team with breathtaking tales
of object storage reliability and easy to use APIs, let's talk downsides.

We once pretended that files were local because NFS and CIFS were usually
local and usually fast.  File in object storage might be in the same
data center on the same network backbone, but they might also be across
the globe.

Object storage systems can handle objects and files that are big and
small, but note that fewer large files will tend to transfer faster
than many tiny files assuming that the total number of bytes
transferred is the same.

Object storage providers promise an incredible amount of durability
when pitching their services.  For instance, Amazon S3 promises
99.999999999% object durability.  That's enough 9's that I went a
little cross eyed counting them, and you'll actually win the PowerBall
before S3 loses one of your objects.  Please don't send me email about
how little I understand statistics, I'm using hyperbole here.  Anyway,
do you want to know how you'll probably end up losing data?  Human
error.  Definitely consider enabling versioning if it is available,
and keep a second copy if not.

Apps that rely on object storage, especially public cloud object
storage, will need to consider security controls on their buckets.  In
the days of NFS, we could at least pretend like bad guys would never
have access to the file server.  Globally distributed object storage
is a service that's publicly accessible on purpose, so any buckets
without security settings can be seen by anyone.

So now we've listed why the old way is terrible, and listed some of
the ways that object storage can bite us.  Are there alternatives to
both that solve all of the problems once and for all?  Nope.  Sorry.
That's the problem with abstractions.

If you really don't want to consider these sorts of tradeoffs, you
could find a sucker on the DBA team.  If you're lucky they'll sign up
to provide a globally replicated and redundant database that is always
up in all locations and never experiences replication lag.  It'll
never work out as planned, and you'll still have problems, but you can
always blame them.
