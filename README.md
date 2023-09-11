# Labb3DesignPatterns
Design patterns assignment

# About the task:

In this task, you will be given an existing simple application that you will further develop by adding more functionality. During this development, you should continue to use the same design patterns that are already used in the application.

# What you need to do:

-  Copy the code below to your project.
    - **Code to Copy** (click the arrow to expand):

```csharp
using System;
using System.Collections.Generic;

namespace VarmDrinkStation
{
    public interface IWarmDrink
    {
        void Consume();
    }
    internal class Water : IWarmDrink
    {
        public void Consume()
        {
            Console.WriteLine("Warm water is served.");
        }
    }
    public interface IWarmDrinkFactory
    {
        IWarmDrink Prepare(int total);
    }
    internal class HotWaterFactory : IWarmDrinkFactory
    {
        public IWarmDrink Prepare(int total)
        {
            Console.WriteLine($"Pour {total} ml hot water in your cup");
            return new Water();
        }
    }
    public class WarmDrinkMachine
    {
        public enum AvailableDrink // violates open-closed
        {
            Coffee, Tea
        }
        private Dictionary<AvailableDrink, IWarmDrinkFactory> factories =
          new Dictionary<AvailableDrink, IWarmDrinkFactory>();

        private List<Tuple<string, IWarmDrinkFactory>> namedFactories =
          new List<Tuple<string, IWarmDrinkFactory>>();

        public WarmDrinkMachine()
        {
            foreach (var t in typeof(WarmDrinkMachine).Assembly.GetTypes())
            {
                if (typeof(IWarmDrinkFactory).IsAssignableFrom(t) && !t.IsInterface)
                {
                    namedFactories.Add(Tuple.Create(
                      t.Name.Replace("Factory", string.Empty), (IWarmDrinkFactory)Activator.CreateInstance(t)));
                }
            }
        }
        public IWarmDrink MakeDrink()
        {
            Console.WriteLine("This is what we serve today:");
            for (var index = 0; index < namedFactories.Count; index++)
            {
                var tuple = namedFactories[index];
                Console.WriteLine($"{index}: {tuple.Item1}");
            }
            Console.WriteLine("Select a number to continue:");
            while (true)
            {
                string s;
                if ((s = Console.ReadLine()) != null
                    && int.TryParse(s, out int i) // c# 7
                    && i >= 0
                    && i < namedFactories.Count)
                {
                    Console.Write("How much: ");
                    s = Console.ReadLine();
                    if (s != null
                        && int.TryParse(s, out int total)
                        && total > 0)
                    {
                        return namedFactories[i].Item2.Prepare(total);
                    }
                }
                Console.WriteLine("Something went wrong with your input, try again.");
            }
        }
    }
    class Program
    {
        static void Main(string[] args)
        {
            var machine = new WarmDrinkMachine();
            IWarmDrink drink = machine.MakeDrink();
            drink.Consume();
        }
    }
}
```

-  First, test that you can run the application in its current state.
-  Then, add functions to prepare/brew coffee, cappuccino, and hot chocolate.
-  You should continue to use the same design patterns already present in the application and expand upon them without adding more design patterns or ceasing to use them.
