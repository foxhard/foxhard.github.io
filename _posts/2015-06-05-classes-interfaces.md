---
layout: post
title:  "An example of convivence between abstract classes and interfaces"
date:   2015-06-05 13:50:39
categories: [coding]
tags: [c#, .net]
---



Some years ago, someone asked me about of when to use an interface and when to use an abstract class. Well, the answer depends of some factors like common behaviors and level of extensibility. I thought it would be a good idea to express my answer by using an example

### The example
For this example, we are going to create an `imaginary concept of social network` so for us a social network is something that can post messages with images and save an history of posted messages.

**Creating a SocialNetwork abstract class**

```csharp
public abstract class SocialNetwork
{
    public List<string> History { get; private set; }

    protected SocialNetwork()
    {
        History = new List<string>();
    }

    public void Post(string comment, byte[] image)
    {
        DoPost(comment, image);
        History.Add(comment);
    }
    
    protected virtual void DoPost(string comment, byte[] image)
    {
    }
}
```

**Adding Facebook and Twitter social networks**
```csharp
public class Facebook : SocialNetwork
{
    protected override void DoPost(string comment, byte[] image)
    {
        //Logic to make a post on facebook
    }
}

public class Twitter : SocialNetwork
{
    protected override void DoPost(string comment, byte[] image)
    {
        //Logic to make a post on twitter
    }
}
```

Fig. 1 Shows a diagram with the classes relationship
![my alternate text](/static/img/posts/2015-06-05-classes-interfaces/initial-design.png)
Fig. 1

Now imagine that, we have a class that can post a message to all of our social networks:

```csharp
public class SocialNetworkProcessor
{
    private readonly SocialNetwork[] _socialNetworks;

    public SocialNetworkProcessor(SocialNetwork[] socialNetworks)
    {
        _socialNetworks = socialNetworks;
    }

    public void Post(string message, byte[] image)
    {
        foreach (var socialNetwork in _socialNetworks)
        {
            socialNetwork.Post(message, image);
        }
    }
}
```

**Putting all together**

```csharp
class Program
{
    static void Main(string[] args)
    {
        var socialNetworks = new SocialNetwork[]
        {
            new Facebook(), 
            new Twitter()
        };

        var processor = new SocialNetworkProcessor(socialNetworks);
        
        var someImage = new byte[] {1, 2, 3};
        processor.Post(&quot;Hello&quot;, someImage);
    }
}
```

So far so good, but the code we have just written above has a serious design problem. Did you see the problem?. Imagine that, we have to handle a totally different kind of social network. E.g. something tha doesn't store messages history, something like `Snapchat`, so our new class will be called `Snapchat`. `Snapchat` class will only keep a track of the latest posted message. Of course, we will pass our class by our `SocialNetWorkProcessor`, in order to post messages by our new class as well. Let me show you:

**Creating Snapchat social network**
```csharp
public class Snapchat : SocialNetwork
{
    private string _lastMessage;

    protected override void DoPost(string comment, byte[] image)
    {
        //Logic to make a post on snapchat
        _lastMessage = comment;
        ProcessLastMessage();
        History.Clear();
    }
    
    private void ProcessLastMessage()
    {
        //Some logic here.
    }
}
```

As you can notice above, `Snapchat` class inherits from `SocialNetwork` class, so `Snapchat` class will store a history of posts too. But we don't want it, so we have to clear the history list

Fig. 2 shows the class diagram including the new `Snapchat` class


![my alternate text](/static/img/posts/2015-06-05-classes-interfaces/next-design-1.png)
Fig. 2

**Interfaces comes in action**

The problem with the implementation shown above is that,  `Snapchat` has a thing that he doesn't need it, i.e. History. Imagine for a moment that, `SocialNetwork` class would has a lot of things like History, image history, timeline generator or whatever necessary for a normal social network. Every new social network that we would want to implent would be forced to inherit all of the current staff in `SocialNetwork` base class. That's why we need a higher level of abstraction there. `SocialNetwork` base class is what we know how a normal social network, but we need a super abstraction to define what a `SocialNetwork` will do without define any behavior for it, so we need to define an `interface`

```csharp
public interface ISocialNetwork
{
    void Post(string message, byte[] image);
}
```

Now, we will do `SocialNetwork` class to implement `ISocialNetwork`

```csharp
public abstract class SocialNetwork : ISocialNetwork
{
    public List<string> History { get; private set; }

    protected SocialNetwork()
    {
        History = new List<string>();
    }

    public void Post(string comment, byte[] image)
    {
        DoPost(comment, image);
        History.Add(comment);
    }
    
    protected virtual void DoPost(string comment, byte[] image)
    {
    }
}
```

Here is the new `Snapchat` class:

```csharp
public class Snapchat : ISocialNetwork
{
    private string _lastMessage;

    public void Post(string message, byte[] image)
    {
        //Logic to do a snapchat post
        _lastMessage = message;
        ProcessLastMessage();
    }

    private void ProcessLastMessage()
    {
        //Some logic here.
    }
}
```

Fig 3. Shows the `SocialNetwork` and `Snapchat` classes implementing `ISocialNetwork`.

![my alternate text](/static/img/posts/2015-06-05-classes-interfaces/next-design-2.png)
Fig. 3

Of course, we need to update our `SocialNetworkProcessor` class by replacing `SocialNetwork` class references by `ISocialNetwork` interface:

```csharp
public class SocialNetworkProcessor
{
    private readonly ISocialNetwork[] _socialNetworks;

    public SocialNetworkProcessor(ISocialNetwork[] socialNetworks)
    {
        _socialNetworks = socialNetworks;
    }

    public void Post(string message, byte[] image)
    {
        foreach (var socialNetwork in _socialNetworks)
        {
            socialNetwork.Post(message, image);
        }
    }
}
```

So far, the design is powerfull enough, `SocialNetworkProcessor` can post in any social network that implements `ISocialNetwork` interface. `Facebook` and `Twitter` share common behavior from `SocialNetwork` and it implements `ISocialNetwork`. `Snapchat` class doesn't share any behavior with `Facebook` and `Twitter` but it is a social network too, so it directly implements `ISocialNetwork` interface

**A new player comes to the game**

Now imagine that, we need to create a new social network e.g. `Sobbr`. `Sobbr` is a social network that only stores your posts by 24 hours. We also need a logic to process the lastPost of `Sobbr` like `Snapchat` does. By taking advantage of our current design, we can easily create a new class to share behavior between `Snapchat` and `Sobbr`, because those are volatile social networks, so our new base class will be called `VolatileSocialNetwork` and of course it will implement our `ISocialNetwork` interface

```csharp
public abstract class VolatileSocialNetwork : ISocialNetwork
{
    private string _lastMessage;

    public void Post(string message, byte[] image)
    {
        //Logic to do a snapchat post
        _lastMessage = message;
        ProcessLastMessage();
    }

    private void ProcessLastMessage()
    {
        //Some logic here.
    }
}
```

**Creating the new class Sobbr**

```csharp
public class Sobbr : VolatileSocialNetwork
{
    private DateTime _creationTime;
    
    private void DestroySocialNetwork()
    {
        //Some logic here to destroy data after 24 hours.
    }
}
```

Of course `Snapchat` will inherit from `VolatileSocialNetwork` too

```csharp
public class Snapchat : VolatileSocialNetwork
{
}
```

Fig 4. Shows the final design with `Facebook` and `Twitter` inherited from `SocialNetwork` base class, also `Snapchat` and `Sobbr` inherited from `VolatileSocialNetwork` base class. `SocialNetwork` and `VolatileSocialNetwork` implement `ISocialNetwork` interface

![my alternate text](/static/img/posts/2015-06-05-classes-interfaces/next-design-3.png)
Fig. 4

**The final**

```csharp
class Program
{
    static void Main(string[] args)
    {
        var socialNetworks = new ISocialNetwork[]
        {
            new Facebook(),
            new Twitter(),
            new Snapchat(),
            new Sobbr()
        };
        var processor = new SocialNetworkProcessor(socialNetworks);
        
        var someImage = new byte[] {1, 2, 3};
        processor.Post("Hello", someImage);
    }
}
```

By following that design, we won't to modify other parts of the code to include more social networks

Thanks for read
