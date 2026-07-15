"""
Анализ базы данных Chinook (SQLite) с помощью SQL, pandas и визуализации.

Датасет: chinook_sqlite.db — учебная база данных музыкального магазина
(таблицы: track, genre, album, artist, customer, invoice и др.)
"""

import sqlite3
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

conn = sqlite3.connect("chinook_sqlite.db")


def task_1_list_tables():
    """Получить список всех таблиц в базе данных."""
    query = "SELECT name FROM sqlite_master WHERE type='table'"
    df_tables = pd.read_sql_query(query, conn)
    print("Таблицы в базе данных:")
    print(df_tables)
    return df_tables


def task_2_explore_tracks():
    """Изучить структуру таблицы track: первые строки, типы данных, пропуски."""
    df_track = pd.read_sql_query("SELECT * FROM track", conn)
    print("\nПервые 5 строк таблицы track:")
    print(df_track.head(5))
    print("\nИнформация о типах данных:")
    print(df_track.info())
    print("\nКоличество пропусков по колонкам:")
    print(df_track.isnull().sum())
    return df_track


def task_3_tracks_by_genre():
    """Количество треков по жанрам (столбчатая диаграмма)."""
    query = """
    SELECT g.Name, COUNT(t.TrackId) AS Total
    FROM genre g
    JOIN track t ON g.GenreId = t.GenreId
    GROUP BY g.Name
    ORDER BY Total DESC
    """
    df_genre = pd.read_sql_query(query, conn)

    plt.figure(figsize=(16, 7))
    plt.bar(df_genre["Name"], df_genre["Total"])
    plt.xticks(rotation=45)
    plt.title("Общее количество треков по жанру")
    plt.xlabel("Названия жанров")
    plt.ylabel("Количество треков")
    plt.tight_layout()
    plt.savefig("genre.png")
    plt.close()

    print(f"\nСамый популярный жанр: {df_genre.iloc[0]['Name']} "
          f"({df_genre.iloc[0]['Total']} треков)")
    return df_genre


def task_4_track_duration_distribution():
    """Распределение длительности треков (гистограмма)."""
    query = "SELECT Milliseconds / 1000.0 AS Seconds FROM Track"
    df = pd.read_sql_query(query, conn)

    plt.figure(figsize=(10, 6))
    sns.histplot(data=df, x="Seconds", kde=True, bins=50)
    plt.title("Распределение длительности треков")
    plt.xlabel("Длительность (секунды)")
    plt.ylabel("Количество треков")
    plt.tight_layout()
    plt.savefig("track_duration.png")
    plt.close()

    mode_bin = df["Seconds"].round(-1).mode()[0]
    print(f"\nНаиболее частая длительность треков: около {mode_bin:.0f} секунд")
    return df


def task_5_avg_duration_by_genre():
    """Средняя длительность треков по жанрам."""
    query = """
    SELECT g.Name,
           AVG(t.Milliseconds) / 1000.0 AS avg_length
    FROM Track t
    JOIN Genre g ON g.GenreId = t.GenreId
    GROUP BY g.Name
    ORDER BY avg_length DESC
    """
    df = pd.read_sql_query(query, conn)

    plt.figure(figsize=(12, 6))
    sns.barplot(data=df, x="Name", y="avg_length", color="skyblue", label="Треки")
    plt.title("Средняя длительность треков по жанру")
    plt.xlabel("Жанр")
    plt.ylabel("Средняя длительность (секунды)")
    plt.xticks(rotation=45)
    plt.grid(axis="y", linestyle="--", alpha=0.7)
    plt.legend()
    plt.tight_layout()
    plt.savefig("avg_length_by_genre.png")
    plt.close()

    print(f"\nЖанр с наибольшей средней длительностью треков: {df.iloc[0]['Name']} "
          f"({df.iloc[0]['avg_length']:.0f} сек)")
    return df


def task_6_album_genre_pivot():
    """Сводная таблица: средняя длительность треков по альбому и жанру."""
    query = """
    SELECT track.TrackId, album.Title AS Album_title,
           genre.Name AS Genre_name, track.Milliseconds
    FROM Track
    JOIN Album ON track.AlbumId = album.AlbumId
    JOIN Genre ON track.GenreId = genre.GenreId
    """
    df_all = pd.read_sql_query(query, conn)

    pivot_df = df_all.pivot_table(
        index="Album_title",
        columns="Genre_name",
        values="Milliseconds",
        aggfunc="mean",
    )

    print("\nПример данных:")
    print(df_all.head())
    print("\nСводная таблица (альбом x жанр, средняя длительность в мс):")
    print(pivot_df.head())
    return df_all, pivot_df


def task_7_top_customers():
    """Топ-5 клиентов по сумме покупок."""
    query = """
    SELECT customer.CustomerId,
           customer.FirstName || ' ' || customer.LastName AS customer_name,
           SUM(invoice.Total) AS total_spent
    FROM customer
    JOIN invoice ON invoice.CustomerId = customer.CustomerId
    GROUP BY customer.CustomerId
    ORDER BY total_spent DESC
    LIMIT 5
    """
    df_top = pd.read_sql_query(query, conn)

    plt.figure(figsize=(8, 5))
    sns.barplot(data=df_top, x="customer_name", y="total_spent",
                hue="customer_name", palette="Blues_r", legend=False)
    plt.title("Топ-5 клиентов по сумме покупок")
    plt.xlabel("ФИО клиента")
    plt.ylabel("Сумма покупок ($)")
    plt.xticks(rotation=45)
    plt.tight_layout()
    plt.savefig("top_customers.png")
    plt.close()

    print(f"\nКлиент с наибольшей суммой покупок: {df_top.iloc[0]['customer_name']} "
          f"(${df_top.iloc[0]['total_spent']:.2f})")
    return df_top


if __name__ == "__main__":
    task_1_list_tables()
    task_2_explore_tracks()
    task_3_tracks_by_genre()
    task_4_track_duration_distribution()
    task_5_avg_duration_by_genre()
    task_6_album_genre_pivot()
    task_7_top_customers()

    conn.close()
    print("\nАнализ завершён. Графики сохранены рядом со скриптом.")
