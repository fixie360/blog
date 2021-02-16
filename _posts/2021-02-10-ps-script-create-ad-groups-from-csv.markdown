---
layout: post
title:  "Creating Active Directory Groups Using PowerShell"
date:   2021-02-16 18:30:00 +1100
category: devops
tags: powershell ad azure sysadmin microsoft
---

As a SysAdmin I'm often required to create Active directory groups for users. In my current role we don't have a way to this programmatically, and given the preference for any administration to be done by PowerShell, this presented the perfect opportunity to make something that will have an ongoing use and impact.

I knew I wanted to start with a CSV file but being a novice with PowerShell I wasn't even sure if this was possible. Of course it is, and it didn't take too much time with internet searches to find what I needed.

One benefit to using a CSV file is that if prepared in advance it could be shown to requestors and other technicians as a confirmation of what's being created. I also wanted to add a reference number to each group created, so anyone looking through AD can see when and why the group was created. This is good for tracking and accountability, tying nicely to an existing helpdesk system.

# Requirements

So the script needs to:
- Read a CSV.
- Create an AD group.
- Populate each new group with specified usernames.
- Add something in the Notes field for each group created.

# Making the Script

I found [Richard Mueller's](https://social.technet.microsoft.com/profile/richard%20mueller/?ws=usercard-mini) comment on a [TechnNet forum post](https://social.technet.microsoft.com/Forums/windowsserver/en-US/f2200d65-a79e-41ae-b9e7-48b4f408b70c/create-active-directory-groups-with-users-from-csv?forum=winserverpowershell) to create and populate groups in PowerShell which had almost everything I needed.

I still wanted to be able to add something in the ``Notes`` field and learned that in AD this is actually known as ``info``. I found this comment from [Chamele0n](https://community.spiceworks.com/people/chamele0n) on a [Spiceworks forum post](https://community.spiceworks.com/topic/1358650-new-adgroup-with-notes-field) which showed how to replace the ``info`` text. Exactly what I needed.

Pulling these together into a single script was simple enough and my first test run worked without issue.

# Improvements

This is a really simple script and as such, there are plenty of improvements to be made from here. It needs some interactivity, currently there's no feedback from the script - it just goes off and does the job and you're left wondering if it's worked or not.

There's a decent foundation to build on here and improve my PowerShell skills at the same time.

TODO:
- When launching the script, giving the user options to choose what Group Scope (``Domain local``, ``Global`` or ``Universal``) and Group Type (``Security``, ``Distribution``).
- Currently, this is for on-prem, but will need some work to make it work in Azure AD.
- Have a sync task if there is more than one Domain Controller in the environment.
- It might be nice to bring up a GUI to select the CSV file to use.
- Interactive selection of Organisational Unit to create objects in, otherwise leave a default.
- Option to input the ``Note`` in case there isn't anything in the CSV.
- Error handling for times when a group is created  that has no users or if a group already exists.
- Error handling in general.
- Plus plenty more I can't think of right now.

# Creating and Populating Active Directory Groups with PowerShell using a CSV File.

The CSV file should have:
- Headers on line one.
- Each group to be created on a new line.
- Groups that have multiple users being added need to have their members wrapped with " ".

For example, your CSV might look like this:

{% highlight csv %}
Name,Description,Note,Members
Group1,Description1,Note1,Member1
Group2,Description2,Note2,"Member1,Member2"
{% endhighlight %}

And finally, the script as follows:

{% highlight PowerShell %}
# Specify where you want the OU created - for the example OU Security Groups in domain.com
$OU = "OU=Security Groups,DC=Domain,DC=com"

# Specify the location of the CSV
$ADGroupsCSV = Import-Csv -Path "c:\spripts\ADGroups.csv"

ForEach ($Line In $ADGroupsCSV)
{
    $GroupName = $Line.Name
    $GroupDescription = $Line.Description

    # This command creates a new Global Security group, setting the Common Name as the sAMAccountName (pre-Windows 2000 name).
    New-ADGroup -Name $GroupName -sAMAccountName $GroupName -GroupCategory Security -GroupScope Global -Path $OU -Description $GroupDescription

    #This command will update each new group with a note, if your domain requires a reference number, for example
    $GroupNote = $line.Notes
    Set-ADGroup -Identity $GroupName -Replace @{info="$($GroupNote)"}

    #Now we parse the user names and add them to the new groups.
    $Members = $Line.Members.Split(",")
    ForEach ($Member in $Members)
    {
        Add-ADGroupMember Identity $GroupName -Members $Member
    }
}
{% endhighlight %}


Thanks for reading.

Trev
