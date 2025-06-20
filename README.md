import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# 1. Import data
df = pd.read_csv('fcc-forum-pageviews.csv', parse_dates=['date'], index_col='date')

# 2. Clean data (remove top and bottom 2.5% of page views)
lower_bound = df['value'].quantile(0.025)
upper_bound = df['value'].quantile(0.975)
df = df[(df['value'] >= lower_bound) & (df['value'] <= upper_bound)]

# 3. Line Plot
def draw_line_plot():
    # Create figure and plot
    fig, ax = plt.subplots(figsize=(15, 5))
    ax.plot(df.index, df['value'], color='red', linewidth=1)
    ax.set_title("Daily freeCodeCamp Forum Page Views 5/2016-12/2019")
    ax.set_xlabel("Date")
    ax.set_ylabel("Page Views")

    # Save and return figure
    fig.savefig('line_plot.png')
    return fig

# 4. Bar Plot
def draw_bar_plot():
    # Prepare data
    df_bar = df.copy()
    df_bar['year'] = df_bar.index.year
    df_bar['month'] = df_bar.index.month_name()

    # Group and reshape data
    df_grouped = df_bar.groupby(['year', 'month'])['value'].mean().unstack()

    # Order months correctly
    month_order = [
        'January', 'February', 'March', 'April', 'May', 'June',
        'July', 'August', 'September', 'October', 'November', 'December'
    ]
    df_grouped = df_grouped[month_order]

    # Create bar plot
    fig = df_grouped.plot(kind='bar', figsize=(15, 10)).figure
    plt.xlabel("Years")
    plt.ylabel("Average Page Views")
    plt.legend(title="Months")
    plt.tight_layout()

    # Save and return figure
    fig.savefig('bar_plot.png')
    return fig

# 5. Box Plots
def draw_box_plot():
    # Prepare data for box plots
    df_box = df.copy()
    df_box.reset_index(inplace=True)
    df_box['year'] = df_box['date'].dt.year
    df_box['month'] = df_box['date'].dt.strftime('%b')
    df_box['month_num'] = df_box['date'].dt.month

    # Sort by month number for correct order
    df_box = df_box.sort_values('month_num')

    # Create figure and subplots
    fig, axes = plt.subplots(1, 2, figsize=(18, 6))

    # Year-wise Box Plot
    sns.boxplot(data=df_box, x='year', y='value', ax=axes[0])
    axes[0].set_title('Year-wise Box Plot (Trend)')
    axes[0].set_xlabel('Year')
    axes[0].set_ylabel('Page Views')

    # Month-wise Box Plot
    sns.boxplot(data=df_box, x='month', y='value', ax=axes[1])
    axes[1].set_title('Month-wise Box Plot (Seasonality)')
    axes[1].set_xlabel('Month')
    axes[1].set_ylabel('Page Views')

    # Save and return figure
    fig.savefig('box_plot.png')
    return fig
# Page-View-Time-Series-Visualizer
