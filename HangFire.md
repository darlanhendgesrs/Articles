## Hangfire

Hangfire is a powerful background job manager that allows you to schedule and manage recurring tasks in your ASPNET Core application. To start using Hangfire, you need to install the Hangfire.AspNetCore package and add it to your project.

1. Install the package: Open the NuGet Package Manager and search for
    Hangfire.AspNetCore. Install it in your project.
    
2.  Add Hangfire to your services: In your `Startup.cs` file, in the `ConfigureServices` method, add the following code:

## After installed all dependencies you must configure the Hangfire inside of the project's startup.

    builder.Services.AddHangfire(x => x.UseSqlServerStorage("connectionString"));
    builder.Services.AddHangfireServer();

## Second part:

 Add Hangfire to the request pipeline: In the `Configure` method, add the following code:

    app.UseHangfireDashboard();

## Creating jobs and registering them

After everything in place it's time to register your jobs. 
Note that I'm registering recurring jobs using the `RecurringJob` class:
Here's an example of how you could create jobs and put them inside of one class:

     public static class Jobs
        {

        public static void Hourly()
        {
            Console.WriteLine("Hello, Hangfire hourly!");
        }

        public static void TenMinutely()
        {
            Console.WriteLine("Hello, Hangfire TenMinutely!");
        }

        public static void EveryMondayAt9am()
        {
            Console.WriteLine("Hello, Hangfire EveryMondayAt9am!");
        }

        public static void TwoMinutes()
        {
            Console.WriteLine("Hello, Hangfire TwoMinutes!");
        }

        public static void RegisterJobs()
        {
            RecurringJob.AddOrUpdate(() => Hourly(), JobSchedules.EveryHour);
            RecurringJob.AddOrUpdate(() => TenMinutely(), JobSchedules.Every10Minutes);
            RecurringJob.AddOrUpdate(() => EveryMondayAt9am(), JobSchedules.EveryMondayAt9am);
            RecurringJob.AddOrUpdate(() => TwoMinutes(), JobSchedules.EveryXMinutes(1));
        }
	}

One important point is JobSchedules is a custom class that I've decided to put my CRON expressions in order to make it easier.

     public class JobSchedules
        {
            public const string EveryMinute = "* * * * *";
            public const string Every10Minutes = "*/10 * * * *";
            public const string EveryHour = "0 * * * *";
            public const string EveryDay = "0 0 * * *";
            public const string EveryMondayAt9am = "0 9 * * 1";
            public const string EveryLastDayOfMonth = "0 0 * L *";
    
            public static string EveryXMinutes(int minutes)
            {
                return $"*/{minutes} * * * *";
            }
    
            public static string EveryXHours(int hours)
            {
                return $"0 */{hours} * * *";
            }
    
        }

Last but not least we need to call the RegisterJobs method:

     Jobs.RegisterJobs();


## Insights:

Creating a job in Hangfire is relatively simple. You can create a background job by calling the `BackgroundJob.Enqueue` method and passing in a method to be executed as a delegate.

Here's an example of how you might create a background job in Hangfire:

    BackgroundJob.Enqueue(() => Console.WriteLine("Hello, Hangfire!"));

This will create a background job that will execute the `Console.WriteLine("Hello, Hangfire!")` method when it runs.

You can also pass any number of arguments to the method, like this:

    `BackgroundJob.Enqueue(() => MyMethod("Hello", "Hangfire"));`

You can also pass an instance method, like this:

    BackgroundJob.Enqueue<MyClass>(x => x.MyMethod("Hello", "Hangfire"));



> Also keep in mind that the cron expressions are based on the crontab
> format and hangfire is based on cron expressions, and it's composed of
> 6 fields separated by white space. These fields represent second
> (optional), minute, hour, day, month, and week. The `*/10` in the
> minute field is the one that makes the job run every 10 minutes.


## Monitoring your jobs


One of the key features of Hangfire is its ability to monitor the status of your jobs, including their execution history, retrying failed jobs, and manually triggering jobs.

To start using Hangfire, you need to install the Hangfire.AspNetCore package and add it to your project. Once you have done this, you can add Hangfire to your services and the request pipeline, and create background jobs.

Monitoring your jobs with Hangfire is easy with the use of the Hangfire Dashboard. This is a user-friendly interface that provides a wealth of information about your jobs, including their status, execution history, and statistics.

Once you have accessed the dashboard, you will be able to view the following information:

-   Job status: You can see the status of each job, whether it is running, succeeded, failed, or enqueued.
-   Job details: You can view the details of each job, including the method it calls, its arguments, and its execution history.
-   Job statistics: You can view statistics about your jobs, such as the number of jobs succeeded, failed, or enqueued.
-   Job control: You can retry failed jobs, delete.
